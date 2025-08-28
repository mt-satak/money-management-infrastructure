---
title: Money Management Infrastructure
layout: default
---

# Money Management Infrastructure

AWS ECS Fargateを使用したマネーマネジメントアプリケーションのインフラ構築プロジェクトです。

## プロジェクト概要

このプロジェクトでは、以下のAWSサービスを活用したスケーラブルなWebアプリケーション基盤を構築します：

- **ECS Fargate**: コンテナオーケストレーション
- **RDS MySQL**: データベース
- **Application Load Balancer**: ロードバランサー
- **CloudFront + S3**: 静的サイトホスティング
- **VPC**: ネットワーク基盤

## アーキテクチャ図

```
Internet → CloudFront → ALB → ECS Fargate → RDS MySQL
                      ↓
                   S3 Static Website (React)
```

## ドキュメント

- [VPC設計解説](vpc-design.html) - ネットワーク基盤の設計思想と構成
- [学習記録](learning-notes.html) - 学習過程で得た知見とメモ
- [デプロイガイド](deployment-guide.html) - 段階的なデプロイメント手順

## 学習目標

### 技術スキル習得
- Infrastructure as Code (AWS CDK)
- コンテナオーケストレーション (ECS)
- ネットワーキング (VPC設計)
- セキュリティ (IAM, Security Groups)
- CI/CD (GitHub Actions)

### 運用スキル習得
- ログ管理・メトリクス監視
- アラート設定・災害復旧
- コスト最適化

## 推定コスト

月額 $58-73 程度を想定

---

最終更新: {{ site.time | date: "%Y年%m月%d日" }}