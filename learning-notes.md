---
title: 学習記録
layout: default
---

# 学習記録

CDK構築過程で得た知見と学習内容をここに記録していきます。

## 2025年8月28日 - プロジェクト開始

### GitHub Pages セットアップ
- `gh-pages`ブランチでドキュメント管理を開始
- Jekyll（Minima theme）を採用
- CDKコードとドキュメントを分離した構成

### VPCの基本概念理解
- VPCは「AWS上の専用ネットワーク環境」
- Public/Privateサブネットの役割分担
- セキュリティグループによる通信制御

### AWS CDK抽象化レベルの理解
- L1 (CFN Resources): CloudFormationの直接マッピング
- L2 (AWS Constructs): ベストプラクティス内蔵の高レベルAPI
- L3 (Patterns): 複数サービス組み合わせのアーキテクチャパターン
- 今回はL2メインで学習効果とベストプラクティスを両立

### 今後の学習予定
- [ ] CDKでのVPC構築実装
- [ ] RDS MySQLの構築
- [ ] ECS Fargateの設定
- [ ] CI/CD パイプライン構築

---

*このページは学習の進捗に応じて更新していきます*

[← トップページに戻る](index.html)