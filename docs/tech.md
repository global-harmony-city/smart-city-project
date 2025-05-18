---
layout: default
title: 技術情報
permalink: /tech/
---

# 技術アーキテクチャ

グローバル・ハーモニー・シティのITシステムは、Microsoft製品エコシステムを活用した効率的かつスケーラブルなアーキテクチャで構築されています。少人数チームでも高品質なシステムを構築・運用できるよう、クラウドサービスとローコード/ノーコードプラットフォームを最大限に活用しています。

## アーキテクチャ概要

<div style="text-align: center; margin: 2rem 0;">
  <img src="{{ site.baseurl }}/assets/architecture.svg" alt="ITアーキテクチャ図" style="max-width: 100%;">
</div>

システムは、以下の5つの層で構成されています：

1. **住民タッチポイント層**: エンドユーザーとのインターフェース
2. **フロントエンド層**: ユーザーインターフェースの実装
3. **アプリケーション層**: ビジネスロジックと機能実装
4. **データ層**: データの保存と管理
5. **インフラ層**: システム基盤と運用環境

## 技術スタックの詳細

### フロントエンド技術

<div class="feature-grid">
  <div class="feature-item">
    <h3>SharePoint Online</h3>
    <p>情報ポータルの基盤として活用。SPFx (SharePoint Framework) を使用したカスタムWebパーツにより、使いやすく魅力的なユーザーインターフェースを提供します。</p>
  </div>
  
  <div class="feature-item">
    <h3>Power Apps</h3>
    <p>住民向けモバイルアプリケーションの開発に使用。Canvas Appによる直感的なUI設計と、フォームベースの申請システムを実現します。</p>
  </div>
  
  <div class="feature-item">
    <h3>Microsoft Teams</h3>
    <p>コミュニティプラットフォームとして活用。チャット、ビデオ会議、ファイル共有などの機能を統合し、住民同士のコラボレーションを促進します。</p>
  </div>
  
  <div class="feature-item">
    <h3>React</h3>
    <p>SPFx WebパーツやTeamsタブアプリの開発に使用。コンポーネントベースの設計により、再利用性と保守性の高いUIを構築します。</p>
  </div>
</div>

### バックエンド技術

<div class="feature-grid">
  <div class="feature-item">
    <h3>Power Automate</h3>
    <p>ワークフロー自動化プラットフォーム。申請承認フロー、通知自動送信、定期レポート生成などの業務プロセスを自動化します。</p>
  </div>
  
  <div class="feature-item">
    <h3>Azure Functions</h3>
    <p>サーバーレスコンピューティングサービス。複雑な処理やカスタムロジックを実装する際に使用し、スケーラブルなバックエンド処理を実現します。</p>
  </div>
  
  <div class="feature-item">
    <h3>Microsoft Graph API</h3>
    <p>Microsoft 365サービス全体にアクセスするための統一API。ユーザー情報、カレンダー、メール、ファイルなど、多様なデータへのアクセスを提供します。</p>
  </div>
  
  <div class="feature-item">
    <h3>Azure Logic Apps</h3>
    <p>クラウドベースのワークフロー統合サービス。外部システムとの連携やデータ変換など、より複雑な統合シナリオに活用します。</p>
  </div>
</div>

### データ管理技術

<div class="feature-grid">
  <div class="feature-item">
    <h3>Microsoft Dataverse</h3>
    <p>Power Platformの中核となるデータストレージサービス。構造化データを安全に管理し、Power AppsやPower Automateと統合します。</p>
  </div>
  
  <div class="feature-item">
    <h3>SharePoint リスト</h3>
    <p>ドキュメントやコンテンツの管理に活用。メタデータ機能を活用した情報分類と検索性の向上を実現します。</p>
  </div>
  
  <div class="feature-item">
    <h3>Azure SQL Database</h3>
    <p>高パフォーマンスのリレーショナルデータベース。大量データの処理や複雑なクエリが必要な場合に活用します。</p>
  </div>
  
  <div class="feature-item">
    <h3>Power BI</h3>
    <p>ビジネスインテリジェンスツール。データの可視化と分析により、意思決定を支援します。</p>
  </div>
</div>

### 認証とセキュリティ

<div class="feature-grid">
  <div class="feature-item">
    <h3>Microsoft Entra ID (Azure AD)</h3>
    <p>クラウドベースのID管理サービス。シングルサインオンや多要素認証を提供し、安全なユーザー認証を実現します。</p>
  </div>
  
  <div class="feature-item">
    <h3>Microsoft Purview</h3>
    <p>データガバナンスとコンプライアンス管理サービス。個人情報の保護と適切なデータライフサイクル管理を支援します。</p>
  </div>
  
  <div class="feature-item">
    <h3>Microsoft Defender</h3>
    <p>統合セキュリティソリューション。脅威検出と対応を自動化し、システム全体のセキュリティ態勢を強化します。</p>
  </div>
</div>

## 開発アプローチ

プロジェクトの開発は、以下の原則に基づいて進められています：

1. **アジャイル開発手法**: 2週間スプリントでの反復的な開発
2. **ローコード優先**: 可能な限りローコード/ノーコードアプローチを採用
3. **コンポーネント再利用**: 標準コンポーネントの作成と再利用による効率化
4. **段階的リリース**: 小規模な機能からリリースし、フィードバックを反映
5. **継続的改善**: ユーザーフィードバックに基づく継続的な改善サイクル

## 段階的実装計画

<div class="progress-timeline">
  <div class="progress-item">
    <h3>フェーズ1（現在）</h3>
    <p>基盤となるプラットフォームの構築と基本機能の実装</p>
    <ul>
      <li>SharePointポータルサイト</li>
      <li>基本的な申請フォーム</li>
      <li>初期認証基盤</li>
    </ul>
  </div>
  
  <div class="progress-item">
    <h3>フェーズ2</h3>
    <p>コア機能の拡充と自動化の強化</p>
    <ul>
      <li>Power Appsモバイルアプリ</li>
      <li>申請ワークフロー自動化</li>
      <li>データ分析基盤</li>
    </ul>
  </div>
  
  <div class="progress-item">
    <h3>フェーズ3</h3>
    <p>高度機能の追加と外部システム連携</p>
    <ul>
      <li>AIを活用した予測分析</li>
      <li>外部APIとの連携</li>
      <li>カスタム開発機能</li>
    </ul>
  </div>
  
  <div class="progress-item">
    <h3>フェーズ4</h3>
    <p>完全統合と高度化</p>
    <ul>
      <li>IoT基盤との連携</li>
      <li>デジタルツイン実装</li>
      <li>自律運用システム</li>
    </ul>
  </div>
</div>

## 開発者向け情報

プロジェクトの開発に参加したい開発者は、以下のリソースをご参照ください：

- [開発者ガイド]({{ site.github.repository_url }}/blob/main/docs/developer-guide.md)
- [技術設計ドキュメント]({{ site.github.repository_url }}/blob/main/docs/technical-design.md)
- [貢献ガイドライン]({{ site.github.repository_url }}/blob/main/CONTRIBUTING.md)

<div style="text-align: center; margin-top: 3rem;">
  <a href="{{ site.github.repository_url }}" class="btn-primary">GitHubリポジトリへ</a>
</div>
