---
title: デプロイガイド
layout: default
---

# デプロイガイド

## 段階的デプロイメント手順

### Phase 1: 基盤構築 (進行中)
1. **VPC・ネットワーク設計** 🚧
   - VPC作成 (10.0.0.0/16)
   - Public/Private subnet設計
   - Internet Gateway, NAT Gateway
   - Security Groups設定

2. **RDS MySQL構築** ⏳
   - MySQLマネージドサービス
   - Multi-AZ対応
   - 自動バックアップ設定
   - セキュリティグループ設定

### Phase 2: コンテナ基盤 (未着手)
3. **ECR (Container Registry)** ⏳
   ```bash
   aws ecr create-repository --repository-name money-management/backend
   aws ecr create-repository --repository-name money-management/frontend
   ```

4. **ECS Cluster構築** ⏳
   - Task Definition作成
   - Service設定
   - オートスケール設定

### Phase 3: CI/CD構築 (未着手)
5. **GitHub Actions → ECR → ECS** ⏳
   - Dockerビルド
   - ECRプッシュ
   - ECS デプロイ

6. **Application Load Balancer** ⏳
   - HTTPS証明書 (ACM)
   - Health Check設定
   - Blue/Green deployments

### Phase 4: フロントエンド (未着手)
7. **S3 + CloudFront** ⏳
   - S3バケット作成
   - CloudFront distribution
   - カスタムドメイン設定

## 推定コスト

| サービス | 仕様 | 月額費用 |
|---------|------|----------|
| ECS Fargate | 0.25 vCPU, 0.5GB | $15 |
| RDS MySQL | db.t3.micro | $15 |
| ALB | - | $18 |
| Data Transfer | - | $5-10 |
| S3 + CloudFront | - | $5 |
| **総計** | | **$58-73/月** |

## 使用技術

### Infrastructure as Code
- **AWS CDK (TypeScript)**: インフラ定義
- **Git**: バージョン管理
- **GitHub Actions**: CI/CD

### AWS Services
- **VPC**: ネットワーク基盤
- **ECS Fargate**: コンテナ実行
- **RDS MySQL**: データベース
- **ALB**: ロードバランサー
- **S3 + CloudFront**: 静的サイトホスティング

---

[← トップページに戻る](index.html)