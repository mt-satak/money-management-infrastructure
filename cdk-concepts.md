---
title: AWS CDK 基礎概念
layout: default
---

# AWS CDK 基礎概念

AWS CDKを効果的に使うための基礎概念と実装パターンについて解説します。

## CDKの抽象化レベル（L1, L2, L3）

AWS CDKには3つの抽象化レベルがあり、それぞれ異なる粒度でAWSリソースを定義できます。

### L1 Constructs（CFN Resources）

**CloudFormationリソースの直接的なマッピング**

```typescript
// 例：VPCのL1実装
const vpc = new ec2.CfnVPC(this, 'MyVPC', {
  cidrBlock: '10.0.0.0/16',
  enableDnsHostnames: true,
  enableDnsSupport: true,
});

const subnet = new ec2.CfnSubnet(this, 'MySubnet', {
  vpcId: vpc.ref,
  cidrBlock: '10.0.1.0/24',
  availabilityZone: 'ap-northeast-1a',
});

const internetGateway = new ec2.CfnInternetGateway(this, 'MyIGW');

const attachment = new ec2.CfnVPCGatewayAttachment(this, 'MyIGWAttachment', {
  vpcId: vpc.ref,
  internetGatewayId: internetGateway.ref,
});
```

**特徴:**
- ✅ **完全制御**: CloudFormationの全機能にアクセス
- ✅ **1:1マッピング**: CloudFormationテンプレートとの対応が明確
- ✅ **細かいカスタマイゼーション**: あらゆる設定を制御可能
- ❌ **記述量多い**: 多くのボイラープレートコード
- ❌ **ベストプラクティス要**: セキュリティ設定等を手動実装
- ❌ **学習コスト高**: CloudFormationの深い知識が必要

**使用場面:**
- 特殊な要件がある場合
- 既存CloudFormationからの移行
- 完全なコントロールが必要な場合

### L2 Constructs（AWS Constructs）

**AWSのベストプラクティスが組み込まれた高レベルAPI**

```typescript
// 例：VPCのL2実装
const vpc = new ec2.Vpc(this, 'MyVPC', {
  ipAddresses: ec2.IpAddresses.cidr('10.0.0.0/16'),
  maxAzs: 2,
  subnetConfiguration: [
    {
      cidrMask: 24,
      name: 'Public',
      subnetType: ec2.SubnetType.PUBLIC,
    },
    {
      cidrMask: 24,
      name: 'Private', 
      subnetType: ec2.SubnetType.PRIVATE_WITH_EGRESS,
    }
  ],
  natGateways: 2,
});

// 自動で作成される：
// - Internet Gateway
// - Route Tables
// - NAT Gateways
// - Security Groups (デフォルト)
```

**特徴:**
- ✅ **ベストプラクティス内蔵**: AWSの推奨設定が自動適用
- ✅ **簡潔な記述**: 少ないコードで複雑なリソースを構築
- ✅ **自動リソース作成**: 関連リソース（IGW、RT等）を自動作成
- ✅ **タイプセーフ**: TypeScriptの型チェックによる安全性
- ✅ **学習しやすい**: 直感的なAPIデザイン
- ⚠️ **カスタマイゼーション制限**: L1より柔軟性は劣る
- ⚠️ **ブラックボックス**: 内部動作の理解が必要

**使用場面:**
- 一般的なユースケース（推奨）
- 学習・プロトタイピング
- ベストプラクティスに従った構築

### L3 Constructs（Patterns）

**複数AWSサービスを組み合わせたアーキテクチャパターン**

```typescript
// 例：ECSパターンのL3実装
const fargateService = new ecsPatterns.ApplicationLoadBalancedFargateService(this, 'MyService', {
  cluster: cluster,
  memoryLimitMiB: 512,
  cpu: 256,
  taskImageOptions: {
    image: ecs.ContainerImage.fromRegistry('my-app:latest'),
    containerPort: 8080,
  },
  publicLoadBalancer: true,
  desiredCount: 2,
});

// 自動で作成される：
// - ECS Cluster
// - Fargate Service
// - Task Definition
// - Application Load Balancer
// - Security Groups
// - IAM Roles
```

**特徴:**
- ✅ **アーキテクチャパターン**: 実証済みの設計パターン
- ✅ **最小コード**: 数行で本格的なサービスを構築
- ✅ **統合済み**: 複数サービスが適切に連携
- ✅ **プロダクション対応**: スケーラビリティ・可用性を考慮
- ❌ **カスタマイゼーション最小**: 柔軟性が最も低い
- ❌ **ブラックボックス化**: 内部構造の把握が困難

**使用場面:**
- 標準的なアーキテクチャパターン
- プロトタイプ・PoC
- 開発速度重視の場合

## レベル選択の指針

### 🏆 推奨：L2メインのハイブリッドアプローチ

```typescript
export class MyInfraStack extends Stack {
  constructor(scope: Construct, id: string, props?: StackProps) {
    super(scope, id, props);

    // L2: 基本構造はベストプラクティスで
    const vpc = new ec2.Vpc(this, 'VPC', {
      ipAddresses: ec2.IpAddresses.cidr('10.0.0.0/16'),
      maxAzs: 2,
    });

    // L2: 標準的なセキュリティグループ
    const webSg = new ec2.SecurityGroup(this, 'WebSG', {
      vpc,
      description: 'Security group for web servers',
      allowAllOutbound: false,
    });

    // L1: 特殊要件がある場合のみ
    const customRule = new ec2.CfnSecurityGroupIngress(this, 'CustomRule', {
      groupId: webSg.securityGroupId,
      ipProtocol: 'tcp',
      fromPort: 8080,
      toPort: 8080,
      sourceSecurityGroupId: albSg.securityGroupId,
      description: 'Custom ALB to App traffic',
    });
  }
}
```

### 判断基準

| 要件 | 推奨レベル | 理由 |
|------|-----------|------|
| 学習・理解 | **L2** | ベストプラクティスを学べる |
| 一般的な構成 | **L2** | 十分な機能と簡潔性 |
| 特殊な要件 | **L1** | 完全なコントロール |
| プロトタイプ | **L3** | 最速で構築 |
| 本番運用 | **L2+L1** | バランスの取れたアプローチ |

## 実装のベストプラクティス

### 1. **段階的な実装**
```typescript
// Step 1: L2で基本構造
const vpc = new ec2.Vpc(this, 'VPC', { ... });

// Step 2: 必要に応じてL1で詳細制御
const customSg = new ec2.CfnSecurityGroup(this, 'CustomSG', { ... });
```

### 2. **命名規則の統一**
```typescript
// 一貫した命名
const vpc = new ec2.Vpc(this, 'MoneyManagementVPC', { ... });
const albSg = new ec2.SecurityGroup(this, 'MoneyManagementAlbSG', { ... });
```

### 3. **設定の外部化**
```typescript
interface VpcConfig {
  cidr: string;
  maxAzs: number;
  natGateways: number;
}

const config: VpcConfig = {
  cidr: '10.0.0.0/16',
  maxAzs: 2,
  natGateways: 2,
};
```

### 4. **リソース間の参照**
```typescript
// L2の参照機能を活用
const database = new rds.DatabaseInstance(this, 'Database', {
  vpc: vpc,                    // VPCを参照
  securityGroups: [dbSg],      // セキュリティグループを参照
  subnetGroup: vpc.isolatedSubnets, // プライベートサブネットを参照
});
```

## まとめ

- **L1**: 完全制御が必要な特殊ケース
- **L2**: 一般的な用途（推奨）
- **L3**: 標準パターンの高速実装

今回のVPC実装では**L2メイン**で進めることで、学習効果とベストプラクティスの習得を両立させます。

---

[← トップページに戻る](index.html)