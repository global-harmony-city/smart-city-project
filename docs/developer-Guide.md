# グローバル・ハーモニー・シティ 開発者ガイド

**文書バージョン**: 1.0.0  
**最終更新日**: 2025年5月18日  
**作成者**: Claude & 技術責任者

## 目次

1. [はじめに](#はじめに)
2. [開発環境のセットアップ](#開発環境のセットアップ)
3. [プロジェクト構造](#プロジェクト構造)
4. [開発ワークフロー](#開発ワークフロー)
5. [フロントエンド開発](#フロントエンド開発)
6. [バックエンド開発](#バックエンド開発)
7. [データモデルの操作](#データモデルの操作)
8. [認証と認可](#認証と認可)
9. [テスト戦略](#テスト戦略)
10. [デプロイメント](#デプロイメント)
11. [トラブルシューティング](#トラブルシューティング)
12. [ベストプラクティス](#ベストプラクティス)
13. [よくある質問](#よくある質問)

## はじめに

### 本ガイドの目的

このガイドは、グローバル・ハーモニー・シティITプロジェクトの開発に参加する開発者向けに、開発環境のセットアップから具体的な実装方法、テスト、デプロイまでの手順を説明するものです。Microsoft製品エコシステムを活用した効率的な開発手法に焦点を当てています。

### 対象読者

- プロジェクトに新しく参加する開発者
- Microsoft 365およびPower Platformを活用した開発に興味のある方
- グローバル・ハーモニー・シティの技術スタックを理解したい方

### 前提知識

本ガイドでは、以下の知識があることを前提としています：

- Web開発の基本知識（HTML, CSS, JavaScript）
- GitとGitHubの基本操作
- クラウドサービスの基本概念
- Microsoft 365サービスの基本知識

Microsoft技術スタックの経験がない方でも理解できるよう、基本的な概念から説明しています。

## 開発環境のセットアップ

### 必要ツール

開発を始めるにあたり、以下のツールの準備が必要です：

| ツール | バージョン | 用途 |
|------|----------|-----|
| [Visual Studio Code](https://code.visualstudio.com/) | 1.82+ | コード編集 |
| [Node.js](https://nodejs.org/) | 18.x LTS | 開発環境実行 |
| [Git](https://git-scm.com/) | 2.40+ | バージョン管理 |
| [PowerShell](https://docs.microsoft.com/ja-jp/powershell/) | 7.2+ | スクリプト実行 |
| [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/) | 最新 | M365管理 |
| [Power Platform CLI](https://docs.microsoft.com/ja-jp/power-platform/developer/cli/introduction) | 最新 | Power Platform開発 |

### Microsoft 365 開発者テナントの取得

1. [Microsoft 365 開発者プログラム](https://developer.microsoft.com/ja-jp/microsoft-365/dev-program)に登録
2. 開発者テナントを作成
3. 開発者サブスクリプションをアクティベート

### Power Platform開発環境の設定

1. [Power Apps](https://make.powerapps.com)にアクセス
2. 「環境」→「+ 新しい環境」で開発環境を作成
   - 名前: DevEnv-YourName
   - 種類: 開発者
   - リージョン: アジアを選択
   - Dataverseデータベースを作成: はい

### リポジトリのクローン

```bash
# GitHubリポジトリのクローン
git clone https://github.com/global-harmony-city/smart-city-project.git
cd smart-city-project

# 依存関係のインストール
npm install
```

### VSCode拡張機能

以下の拡張機能をインストールすることをお勧めします：

- [SPFx Task Runner](https://marketplace.visualstudio.com/items?itemName=ManuelPietschmann.SPFxTaskRunner)
- [Power Platform Tools](https://marketplace.visualstudio.com/items?itemName=microsoft-IsvExpTools.powerplatform-vscode)
- [Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)
- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

### デバッグ環境の構成

VSCodeでのデバッグ設定（`.vscode/launch.json`）:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "SPFx Workbench",
      "type": "pwa-chrome",
      "request": "launch",
      "url": "https://your-tenant.sharepoint.com/_layouts/15/workbench.aspx",
      "webRoot": "${workspaceRoot}/src/spfx",
      "sourceMaps": true,
      "sourceMapPathOverrides": {
        "webpack:///.././*": "${webRoot}/*",
        "webpack:///../*": "${webRoot}/../*"
      }
    },
    {
      "name": "Power Apps",
      "type": "pwa-chrome",
      "request": "launch",
      "url": "https://make.powerapps.com/environments/your-environment-id/apps",
      "webRoot": "${workspaceRoot}/src/powerapps"
    }
  ]
}
```

## プロジェクト構造

### ディレクトリ構造

```
smart-city-project/
├── .github/                  # GitHub関連ファイル
│   ├── workflows/            # GitHub Actionsワークフロー
│   └── ISSUE_TEMPLATE/       # Issue・PR用テンプレート
├── docs/                     # ドキュメント
│   ├── assets/               # 画像やダイアグラム
│   ├── technical-design.md   # 技術設計ドキュメント
│   └── developer-guide.md    # 開発者ガイド
├── scripts/                  # スクリプト
│   ├── setup/                # セットアップスクリプト
│   └── deploy/               # デプロイスクリプト
├── src/                      # ソースコード
│   ├── spfx/                 # SharePoint Frameworkプロジェクト
│   ├── powerapps/            # Power Appsソリューションファイル
│   ├── flows/                # Power Automateフローテンプレート
│   └── azure/                # Azureリソース関連コード
├── config/                   # 設定ファイル
│   ├── sharepoint-template.json  # SharePoint設定テンプレート
│   └── powerapps-template.json   # PowerApps設定テンプレート
├── assets/                   # 静的アセット
├── .gitignore                # Gitで無視するファイル
├── package.json              # npmパッケージ設定
└── README.md                 # プロジェクト概要
```

### 重要なファイル

| ファイル | 説明 |
|--------|------|
| `package.json` | NPMパッケージ定義とスクリプト |
| `config/sharepoint-template.json` | SharePoint設定テンプレート |
| `src/spfx/config/package-solution.json` | SPFxソリューション設定 |
| `src/powerapps/solution.xml` | Power Appsソリューション定義 |
| `scripts/setup/dev-setup.ps1` | 開発環境セットアップスクリプト |

## 開発ワークフロー

### ブランチ戦略

GitHub Flowに基づいたシンプルなブランチ戦略を採用しています：

1. `main`ブランチは常にデプロイ可能な状態を維持
2. 新機能開発は`feature/機能名`ブランチで実施
3. バグ修正は`fix/問題内容`ブランチで実施
4. 機能完成後はPull Requestを作成
5. コードレビュー後に`main`へマージ

### コミットメッセージ規約

コミットメッセージは以下の形式に従ってください：

```
種類(スコープ): メッセージ

詳細な説明（必要な場合）
```

種類:
- `feat`: 新機能
- `fix`: バグ修正
- `docs`: ドキュメントのみの変更
- `style`: コードの意味に影響しない変更
- `refactor`: リファクタリング
- `test`: テスト関連
- `chore`: ビルドプロセスや補助ツールの変更

例:
```
feat(portal): ニュース一覧Webパーツを追加

- 最新5件のニュースを表示
- カテゴリによるフィルタリング機能
- レスポンシブデザイン対応
```

### 作業フロー

1. 最新の`main`ブランチを取得
   ```bash
   git checkout main
   git pull origin main
   ```

2. 作業用ブランチを作成
   ```bash
   git checkout -b feature/news-webpart
   ```

3. 開発作業（コーディング、テスト）

4. 変更をコミット
   ```bash
   git add .
   git commit -m "feat(portal): ニュース一覧Webパーツを追加"
   ```

5. 作業ブランチをリモートにプッシュ
   ```bash
   git push origin feature/news-webpart
   ```

6. GitHubでPull Requestを作成

7. コードレビュー、議論、修正

8. マージ後、ローカルの`main`ブランチを更新
   ```bash
   git checkout main
   git pull origin main
   ```

## フロントエンド開発

### SharePoint Framework (SPFx) 開発

#### 新しいSPFx Webパーツの作成

1. SPFxプロジェクトディレクトリに移動
   ```bash
   cd src/spfx
   ```

2. 新しいWebパーツの作成
   ```bash
   yo @microsoft/sharepoint --solution-name "city-portal-webparts" --framework "react" --component-type "webpart" --component-name "NewsWebPart" --skip-install
   ```

3. 依存関係のインストール
   ```bash
   npm install
   ```

4. ローカルでの開発サーバー起動
   ```bash
   gulp serve
   ```

#### Reactコンポーネント構造

SPFxのReactコンポーネントは以下の構造で実装します：

```tsx
// NewsWebPart.tsx
import * as React from 'react';
import { INewsWebPartProps } from './INewsWebPartProps';
import { NewsItem } from '../../models/NewsItem';
import { NewsService } from '../../services/NewsService';

export interface INewsWebPartState {
  newsItems: NewsItem[];
  loading: boolean;
  error: string | null;
}

export default class NewsWebPart extends React.Component<INewsWebPartProps, INewsWebPartState> {
  private newsService: NewsService;
  
  constructor(props: INewsWebPartProps) {
    super(props);
    this.state = {
      newsItems: [],
      loading: true,
      error: null
    };
    this.newsService = new NewsService();
  }
  
  public async componentDidMount(): Promise<void> {
    try {
      const newsItems = await this.newsService.getLatestNews(this.props.count);
      this.setState({
        newsItems,
        loading: false
      });
    } catch (error) {
      this.setState({
        loading: false,
        error: 'ニュースの読み込みに失敗しました'
      });
    }
  }
  
  public render(): React.ReactElement<INewsWebPartProps> {
    const { loading, error, newsItems } = this.state;
    
    if (loading) {
      return <div>読み込み中...</div>;
    }
    
    if (error) {
      return <div className="error">{error}</div>;
    }
    
    return (
      <div className="news-webpart">
        <h2 className="news-title">{this.props.title}</h2>
        <div className="news-items">
          {newsItems.map(item => (
            <div key={item.id} className="news-item">
              <h3>{item.title}</h3>
              <p className="news-date">{new Date(item.publishedDate).toLocaleDateString()}</p>
              <p>{item.summary}</p>
              <a href={item.url}>詳細を見る</a>
            </div>
          ))}
        </div>
      </div>
    );
  }
}
```

#### Microsoft Graph APIの利用

Microsoft Graph APIを使用してSharePointリストからデータを取得する例：

```typescript
// NewsService.ts
import { MSGraphClient } from '@microsoft/sp-http';
import { WebPartContext } from '@microsoft/sp-webpart-base';
import { NewsItem } from '../models/NewsItem';

export class NewsService {
  private context: WebPartContext;
  
  constructor(context: WebPartContext) {
    this.context = context;
  }
  
  public async getLatestNews(count: number): Promise<NewsItem[]> {
    try {
      const client = await this.context.msGraphClientFactory.getClient();
      
      const response = await client
        .api('/sites/root/lists/News/items')
        .expand('fields')
        .top(count)
        .orderby('fields/PublishedDate desc')
        .get();
      
      return response.value.map(item => ({
        id: item.id,
        title: item.fields.Title,
        summary: item.fields.Summary,
        publishedDate: item.fields.PublishedDate,
        url: item.fields.LinkUrl || `${this.context.pageContext.web.absoluteUrl}/SitePages/News/${item.fields.FileLeafRef}.aspx`
      }));
    } catch (error) {
      console.error('Error fetching news:', error);
      throw error;
    }
  }
}
```

#### フロントエンドデザインガイドライン

- Fluent UIコンポーネントを活用（Microsoft標準のUIライブラリ）
- レスポンシブデザインを必ず実装（モバイル対応）
- アクセシビリティ対応（WCAG 2.1 AAレベル準拠）
- 多言語対応の仕組みを組み込む
- ダークモード/ライトモード両対応

### Power Apps 開発

#### Canvas App 開発

1. [Power Apps](https://make.powerapps.com)にアクセス
2. 「アプリ」→「新しいアプリ」→「キャンバス」→「空白アプリ」を選択
3. 画面設計とフォーミュラの実装

#### Power Apps コンポーネントライブラリ

再利用可能なコンポーネントをライブラリ化することで開発効率を高めます：

1. コンポーネントライブラリの作成
   - 「コンポーネント」→「新しいコンポーネントライブラリ」を選択
   - 基本コンポーネント（ヘッダー、フッター、ナビゲーションなど）を実装

2. コンポーネントの使用
   - アプリに「コンポーネント」→「既存のコンポーネントを追加」でインポート
   - アプリ内で配置して利用

#### Power Apps サンプルコード（ニュース表示画面）

```powerapps
// 画面1 (ニュース一覧)
Screen1 As screen:
    Fill: RGBA(249, 250, 251, 1)
    OnVisible: Set(varNewsItems, Filter(News, IsVisible = true))

    HeaderComponent As Header:
        Title: "ニュース一覧"
        BackButton: false

    Gallery1 As gallery:
        Items: varNewsItems
        Layout: Layout.Vertical
        TemplateFill: RGBA(255, 255, 255, 1)
        TemplateSize: 120
        X: 0
        Y: HeaderComponent.Height
        Width: Parent.Width
        Height: Parent.Height - HeaderComponent.Height
        
        Title_Label As label:
            Text: ThisItem.Title
            Font: Font.'Segoe UI'
            FontWeight: FontWeight.Bold
            Size: 16
            X: 16
            Y: 12
            Width: Parent.Width - 32
        
        Date_Label As label:
            Text: Text(ThisItem.PublishedDate, "[$-ja-JP]yyyy年MM月dd日")
            Font: Font.'Segoe UI'
            Color: RGBA(107, 114, 128, 1)
            Size: 12
            X: 16
            Y: Title_Label.Y + Title_Label.Height + 4
        
        Summary_Label As label:
            Text: ThisItem.Summary
            Font: Font.'Segoe UI'
            Size: 14
            X: 16
            Y: Date_Label.Y + Date_Label.Height + 4
            Width: Parent.Width - 32
            Height: 40
            Overflow: Overflow.Hidden
        
        NextArrow_Icon As icon:
            Icon: Icon.ChevronRight
            X: Parent.Width - 40
            Y: 40
            Color: RGBA(99, 102, 241, 1)
        
        OnSelect: Navigate(Screen2, ScreenTransition.Fade, {varSelectedNews: ThisItem})
```

## バックエンド開発

### Power Automate フローの作成

#### 申請承認フロー

1. [Power Automate](https://flow.microsoft.com)にアクセス
2. 「マイフロー」→「+ 新しいフロー」→「自動化されたクラウドフロー」を選択
3. トリガーとして「PowerAppsがフローを実行するとき」を選択
4. 以下のアクションを追加：
   - 「申請データを取得」：Power Appsからの入力を受け取る
   - 「申請レコードの作成」：Dataverseに申請レコードを作成
   - 「承認を開始」：承認者にタスクを割り当て
   - 「条件」：承認結果に基づいて分岐
   - 「承認結果の更新」：申請ステータスの更新
   - 「メール通知の送信」：申請者への結果通知

#### フローのエクスポート/インポート

1. フローのエクスポート
   - フロー画面で「...」→「エクスポート」→「パッケージ (.zip)」を選択
   - 環境変数を設定してエクスポート

2. フローのインポート
   - 「マイフロー」→「インポート」→パッケージを選択
   - 接続の更新と環境変数の設定

#### Azure Functions 拡張

より複雑な処理が必要な場合、Azure Functionsを使用します：

```javascript
// Azure Function (Node.js) - 住民データ集計処理
module.exports = async function (context, req) {
    try {
        // Dataverse接続取得
        const dataverse = context.bindings.dataverse;
        
        // 住民データの取得
        const residents = await dataverse.fetchAll("cr7e2_residents");
        
        // 集計処理
        const analytics = {
            totalCount: residents.length,
            byAge: {
                under18: residents.filter(r => r.cr7e2_age < 18).length,
                age18to30: residents.filter(r => r.cr7e2_age >= 18 && r.cr7e2_age <= 30).length,
                age31to50: residents.filter(r => r.cr7e2_age >= 31 && r.cr7e2_age <= 50).length,
                age51to65: residents.filter(r => r.cr7e2_age >= 51 && r.cr7e2_age <= 65).length,
                over65: residents.filter(r => r.cr7e2_age > 65).length
            },
            byRegistrationDate: groupByMonth(residents, 'cr7e2_registrationdate')
        };
        
        context.res = {
            status: 200,
            body: analytics,
            headers: {
                'Content-Type': 'application/json'
            }
        };
    } catch (error) {
        context.log.error('Error processing resident data:', error);
        context.res = {
            status: 500,
            body: { error: "処理中にエラーが発生しました" }
        };
    }
};

function groupByMonth(items, dateField) {
    const result = {};
    const now = new Date();
    const sixMonthsAgo = new Date();
    sixMonthsAgo.setMonth(now.getMonth() - 6);
    
    // 過去6ヶ月分の集計
    for (let i = 0; i <= 6; i++) {
        const monthDate = new Date();
        monthDate.setMonth(now.getMonth() - i);
        const yearMonth = `${monthDate.getFullYear()}-${String(monthDate.getMonth() + 1).padStart(2, '0')}`;
        result[yearMonth] = 0;
    }
    
    // データ集計
    items.forEach(item => {
        const date = new Date(item[dateField]);
        if (date >= sixMonthsAgo) {
            const yearMonth = `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(2, '0')}`;
            if (result[yearMonth] !== undefined) {
                result[yearMonth]++;
            }
        }
    });
    
    return result;
}
```

### Custom Connectors

外部システムとの連携が必要な場合、Custom Connectorsを作成します：

1. [Power Platform管理センター](https://admin.powerplatform.microsoft.com)にアクセス
2. 「カスタムコネクタ」→「新しいカスタムコネクタ」を選択
3. OpenAPI定義のインポートまたは手動で設計
4. 認証・アクションの設定
5. コネクタのテストと保存

## データモデルの操作

### Dataverse モデル駆動型開発

#### エンティティ（テーブル）設計

1. [Power Apps](https://make.powerapps.com)にアクセス
2. 「テーブル」→「+ 新しいテーブル」を選択
3. テーブル情報を入力：
   - 表示名: 申請
   - 複数形名: 申請
   - 説明: 住民からの各種申請を管理
4. プライマリ列情報を入力：
   - 表示名: 件名
   - 名前: Title
   - データ型: 一行テキスト
5. 「保存」をクリック

#### 列（フィールド）の追加

1. 作成したテーブルを選択
2. 「+ 新しい列」をクリック
3. 列情報を入力：
   - 表示名: 提出日
   - 名前: SubmissionDate
   - データ型: 日付と時刻
   - 形式: 日付のみ
   - 既定値: 現在の日付と時刻
4. 「保存」をクリック

#### リレーションシップの設定

1. 「リレーションシップ」タブを選択
2. 「+ 新しいリレーションシップ」→「多対1」を選択
3. リレーションシップ情報を入力：
   - 関連付けるテーブル: 住民（リレーションシップの多側）
   - 検索列名: 申請者
   - 現在のテーブルで列を表示: はい
4. 「完了」をクリック

### Power Apps 用データソース

#### SharePointリストの作成と連携

1. SharePointサイトにアクセス
2. 「+ 新規」→「リスト」を選択
3. リスト情報を入力：
   - 名前: イベント情報
   - 説明: コミュニティイベント情報管理
4. 列を追加：
   - イベント名（一行テキスト）
   - 開催日時（日付と時刻）
   - 場所（一行テキスト）
   - 説明（複数行テキスト）
   - カテゴリ（選択肢）
   - イメージURL（一行テキスト）
5. Power Appsでデータソースとして接続：
   - 「データ」→「データソースの追加」→「SharePoint」を選択
   - サイトURLを入力し、「イベント情報」リストを選択

#### Dataverseとの連携

Power Appsから直接Dataverseテーブルを操作する例：

```powerapps
// データソースとしてDataverseテーブルを追加
// 申請テーブル = cr7e2_Applications
// 住民テーブル = cr7e2_Residents

// 新規申請レコードの作成
Patch(
    'cr7e2_Applications',
    Defaults('cr7e2_Applications'),
    {
        'cr7e2_name': txtTitle.Text,
        'cr7e2_description': txtDescription.Text,
        'cr7e2_submissiondate': Now(),
        'cr7e2_status': 1, // 1=提出済
        'cr7e2_applicant@odata.bind': "/cr7e2_residents(" & CurrentUser.ResidentId & ")"
    }
);

// 申請一覧の取得と表示
ClearCollect(
    colMyApplications,
    Filter(
        'cr7e2_Applications',
        'cr7e2_applicant'.'cr7e2_residentid' = CurrentUser.ResidentId
    )
);
```

## 認証と認可

### Microsoft Entra ID (Azure AD) 認証

#### アプリケーション登録の設定

1. [Azure Portal](https://portal.azure.com)にアクセス
2. 「Azure Active Directory」→「アプリの登録」→「新規登録」を選択
3. アプリケーション情報を入力：
   - 名前: グローバル・ハーモニー・シティ ポータル
   - サポートされているアカウントの種類: この組織ディレクトリのみ
   - リダイレクトURI: Web - https://your-tenant.sharepoint.com/sites/city-portal
4. 「登録」をクリック
5. 「API権限」で必要な権限を追加：
   - Microsoft Graph: User.Read, Sites.Read.All
   - SharePoint: Sites.Read.All

#### SPFxでの認証利用

SPFxアプリケーションでAzure ADを使用した認証：

```typescript
// AuthService.ts
import { MSGraphClient } from '@microsoft/sp-http';
import { WebPartContext } from '@microsoft/sp-webpart-base';
import { User } from '@microsoft/microsoft-graph-types';

export class AuthService {
  private context: WebPartContext;
  
  constructor(context: WebPartContext) {
    this.context = context;
  }
  
  public async getCurrentUser(): Promise<User> {
    try {
      const client = await this.context.msGraphClientFactory.getClient();
      const user = await client.api('/me').get();
      return user;
    } catch (error) {
      console.error('Error getting current user:', error);
      throw error;
    }
  }
  
  public async hasRequiredRole(requiredRole: string): Promise<boolean> {
    try {
      const user = await this.getCurrentUser();
      const userRoles = await this.getUserRoles(user.id);
      return userRoles.includes(requiredRole);
    } catch (error) {
      console.error('Error checking user roles:', error);
      return false;
    }
  }
  
  private async getUserRoles(userId: string): Promise<string[]> {
    try {
      const client = await this.context.msGraphClientFactory.getClient();
      const roles = await client.api(`/users/${userId}/memberOf`).get();
      return roles.value.map(role => role.displayName);
    } catch (error) {
      console.error('Error getting user roles:', error);
      return [];
    }
  }
}
```

#### Power Apps での認証

Power Appsでユーザーコンテキストを取得・利用：

```powerapps
// 現在のユーザー情報の取得
Set(
    varCurrentUser,
    {
        DisplayName: User().FullName,
        Email: User().Email,
        AzureId: User().UserId,
        IsAdmin: User().IsInRole("Admin")
    }
);

// 条件付き表示 - 管理者のみ表示される管理パネル
AdminPanel.Visible = varCurrentUser.IsAdmin;
```
## テスト戦略

### テスト種類と方法論

グローバル・ハーモニー・シティプロジェクトでは、以下のテストアプローチを採用しています：

| テスト種類 | 目的 | 実施タイミング | 担当者 |
|---------|------|------------|-------|
| ユニットテスト | 個別コンポーネントの機能検証 | コード作成時 | 開発者 |
| 統合テスト | コンポーネント間の連携検証 | 機能実装完了時 | 開発者 |
| ユーザー受け入れテスト | 要件に対する適合性確認 | リリース前 | 機能担当者 |
| アクセシビリティテスト | アクセシビリティ対応の確認 | UI実装完了時 | 開発者/QA |
| パフォーマンステスト | 応答性能・負荷耐性の検証 | 大きな機能追加時 | 開発者 |

### SPFx コンポーネントのテスト

#### Jest を使用したユニットテスト

SPFxプロジェクトでJestテスト環境をセットアップ：

```bash
# テスト関連パッケージのインストール
npm install --save-dev jest @types/jest ts-jest identity-obj-proxy @testing-library/react @testing-library/jest-dom
```

`jest.config.js`の設定:

```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  moduleNameMapper: {
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
  },
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  testPathIgnorePatterns: ['/node_modules/', '/lib/'],
  collectCoverage: true,
  collectCoverageFrom: ['src/**/*.{ts,tsx}', '!src/**/*.d.ts'],
};
```

`jest.setup.js`の設定:

```javascript
// Mock the SP context and other global objects
global.SP = {
  ClientContext: jest.fn(),
};

// Add testing-library jest-dom matchers
import '@testing-library/jest-dom';
```

Reactコンポーネントのテスト例:

```tsx
// NewsWebPart.test.tsx
import * as React from 'react';
import { render, screen, waitFor } from '@testing-library/react';
import NewsWebPart from '../components/NewsWebPart';
import { NewsService } from '../services/NewsService';

// Mock the news service
jest.mock('../services/NewsService');
const MockNewsService = NewsService as jest.MockedClass<typeof NewsService>;

describe('NewsWebPart', () => {
  beforeEach(() => {
    // Reset mock before each test
    MockNewsService.mockClear();
    
    // Setup mock implementation
    MockNewsService.prototype.getLatestNews.mockResolvedValue([
      { id: '1', title: 'テストニュース1', summary: '概要1', publishedDate: '2025-05-01', url: 'https://example.com/news/1' },
      { id: '2', title: 'テストニュース2', summary: '概要2', publishedDate: '2025-05-02', url: 'https://example.com/news/2' }
    ]);
  });
  
  it('ニュースアイテムをロードして表示する', async () => {
    // Render component
    render(<NewsWebPart title="最新ニュース" count={5} />);
    
    // Initially shows loading
    expect(screen.getByText('読み込み中...')).toBeInTheDocument();
    
    // Wait for data to load
    await waitFor(() => {
      expect(screen.getByText('テストニュース1')).toBeInTheDocument();
      expect(screen.getByText('テストニュース2')).toBeInTheDocument();
      expect(screen.getByText('概要1')).toBeInTheDocument();
      expect(screen.getByText('概要2')).toBeInTheDocument();
    });
    
    // Check title
    expect(screen.getByText('最新ニュース')).toBeInTheDocument();
    
    // Verify service was called correctly
    expect(MockNewsService.prototype.getLatestNews).toHaveBeenCalledWith(5);
  });
  
  it('エラー発生時にエラーメッセージを表示する', async () => {
    // Override mock to throw error
    MockNewsService.prototype.getLatestNews.mockRejectedValue(new Error('API error'));
    
    // Render component
    render(<NewsWebPart title="最新ニュース" count={5} />);
    
    // Wait for error message to appear
    await waitFor(() => {
      expect(screen.getByText('ニュースの読み込みに失敗しました')).toBeInTheDocument();
    });
  });
});
```

### Power Apps テスト

#### Test Studio を使用した自動テスト

Power Apps Test Studioを使用したUIテストの作成:

1. Test Studioを開く
   - Power Appsエディタで「...」→「テスト」→「Test Studioを開く」を選択

2. テストケースの記録
   - 「新しいテストケース」→「記録」をクリック
   - アプリを操作してテスト手順を記録
   - 検証ポイントを追加：「検証を追加」→確認したい要素を選択

3. テスト実行
   - 「テスト実行」をクリックしてテストを実行
   - 結果レポートを確認

#### 手動テストチェックリスト

アプリの主要機能に対するテストチェックリスト例:

```markdown
# ニュースアプリテストチェックリスト

## 1. ニュース一覧表示
- [ ] 最新のニュースアイテムが表示される
- [ ] 正しい並び順（新しい順）で表示される
- [ ] カテゴリフィルターが機能する
- [ ] 画像が適切に表示される
- [ ] タイトル、日付、概要が正しく表示される

## 2. ニュース詳細表示
- [ ] 詳細ページに正しい内容が表示される
- [ ] 添付ファイルがダウンロード可能
- [ ] 関連リンクが機能する
- [ ] バックボタンで一覧に戻れる

## 3. アクセシビリティ
- [ ] スクリーンリーダーでコンテンツが読み上げられる
- [ ] タブキー操作で全機能にアクセス可能
- [ ] コントラスト比が適切である
- [ ] 代替テキストが画像に設定されている

## 4. レスポンシブデザイン
- [ ] スマートフォン表示で問題ない
- [ ] タブレット表示で問題ない
- [ ] デスクトップ表示で問題ない
- [ ] 横/縦向き両方で正しく表示される
```

### テスト自動化とCI/CD統合

#### GitHub Actions ワークフロー

CI/CDパイプラインにテストを組み込むGitHub Actionsの設定例:

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Setup Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '18'
        
    - name: Install dependencies
      run: npm ci
      working-directory: ./src/spfx
      
    - name: Run tests
      run: npm test
      working-directory: ./src/spfx
      
    - name: Build SPFx package
      run: |
        npm run build
        gulp bundle --ship
        gulp package-solution --ship
      working-directory: ./src/spfx
      
    - name: Upload build artifacts
      uses: actions/upload-artifact@v2
      with:
        name: spfx-package
        path: ./src/spfx/sharepoint/solution/*.sppkg
        
  power-platform-solution:
    runs-on: windows-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Install Power Platform CLI
      run: |
        npm install -g @microsoft/powerplatform-cli
        
    - name: Authenticate with Power Platform
      run: |
        pac auth create --url ${{ secrets.ENVIRONMENT_URL }} --username ${{ secrets.PP_USERNAME }} --password ${{ secrets.PP_PASSWORD }}
        
    - name: Build and pack solution
      run: |
        pac solution pack --zipfile solution.zip --folder src/powerapps
        
    - name: Upload Power Platform solution
      uses: actions/upload-artifact@v2
      with:
        name: power-platform-solution
        path: solution.zip
```

## デプロイメント

### 環境戦略

グローバル・ハーモニー・シティプロジェクトでは、以下の環境戦略を採用しています:

| 環境 | 目的 | 更新頻度 | アクセス権限 |
|-----|-----|---------|-----------|
| 開発 (DEV) | 個人開発作業 | 随時 | 開発者のみ |
| テスト (TEST) | 統合テスト | 機能完成時 | 開発者・テスター |
| ステージング (STAGING) | UAT・最終確認 | リリース前 | 開発者・テスター・機能担当者 |
| 本番 (PROD) | 実サービス提供 | 計画リリース時 | すべてのユーザー |

### SharePoint ソリューションのデプロイ

#### SPFx パッケージの作成

```bash
# プロジェクトディレクトリに移動
cd src/spfx

# 本番用ビルド
gulp bundle --ship
gulp package-solution --ship
```

これにより、`sharepoint/solution/`ディレクトリに`.sppkg`ファイルが生成されます。

#### アプリカタログへのデプロイ

1. SharePoint管理センターにアクセス
2. 「アプリ」→「アプリカタログ」を選択
3. 「アプリのアップロード」をクリック
4. `.sppkg`ファイルをアップロード
5. 信頼ダイアログで「デプロイ」をクリック

#### サイトへのアプリ追加

1. 対象のSharePointサイトにアクセス
2. 「サイトコンテンツ」→「+ 新規」→「アプリ」を選択
3. アプリカタログからデプロイした`.sppkg`アプリを選択
4. 「追加」をクリックして完了

### Power Platform ソリューションのデプロイ

#### ソリューションのエクスポート

1. Power Appsにアクセス
2. 「ソリューション」→対象ソリューションを選択
3. 「エクスポート」をクリック
4. エクスポートオプションを設定:
   - 「マネージド」または「アンマネージド」を選択
   - バージョン番号を設定
5. 「エクスポート」をクリックしてZIPファイルをダウンロード

#### ソリューションのインポート

1. 対象環境のPower Appsにアクセス
2. 「ソリューション」→「インポート」をクリック
3. エクスポートしたZIPファイルを選択
4. インポート設定を構成:
   - 接続参照を更新
   - 環境変数を設定
5. 「インポート」をクリックして完了

#### Power Platform CLIを使用した自動デプロイ

```powershell
# 認証
pac auth create -n DevAuth -u https://orgname.crm.dynamics.com -p password

# ソリューションのエクスポート (開発環境から)
pac solution export -n CityPortalSolution -p ./solution.zip -a

# ターゲット環境へのインポート
pac solution import -p ./solution.zip -a

# 管理ソリューションとしてインポート
pac solution import -p ./solution.zip -a -s
```

### Azure リソースのデプロイ

Azure関連リソースのデプロイには、ARM (Azure Resource Manager) テンプレートを使用します:

```json
// azuredeploy.json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "functionAppName": {
      "type": "string",
      "metadata": {
        "description": "Function Appの名前"
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "ストレージアカウントの名前"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "リソースのロケーション"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
    },
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2021-02-01",
      "name": "[concat(parameters('functionAppName'), '-plan')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Y1",
        "tier": "Dynamic"
      },
      "properties": {
        "reserved": true
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2021-02-01",
      "name": "[parameters('functionAppName')]",
      "location": "[parameters('location')]",
      "kind": "functionapp",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', concat(parameters('functionAppName'), '-plan'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
      ],
      "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', concat(parameters('functionAppName'), '-plan'))]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "AzureWebJobsStorage",
              "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', parameters('storageAccountName'), ';EndpointSuffix=', environment().suffixes.storage, ';AccountKey=', listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-04-01').keys[0].value)]"
            },
            {
              "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
              "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', parameters('storageAccountName'), ';EndpointSuffix=', environment().suffixes.storage, ';AccountKey=', listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-04-01').keys[0].value)]"
            },
            {
              "name": "FUNCTIONS_EXTENSION_VERSION",
              "value": "~4"
            },
            {
              "name": "FUNCTIONS_WORKER_RUNTIME",
              "value": "node"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "functionAppUrl": {
      "type": "string",
      "value": "[concat('https://', parameters('functionAppName'), '.azurewebsites.net')]"
    }
  }
}
```

デプロイの実行:

```powershell
# Azureへのデプロイ
az login
az group create --name CityPortalResourceGroup --location japaneast
az deployment group create --resource-group CityPortalResourceGroup --template-file azuredeploy.json --parameters functionAppName=city-portal-functions storageAccountName=cityportalstorage
```

## トラブルシューティング

### 一般的な問題と解決方法

#### SPFx関連の問題

| 問題 | 考えられる原因 | 解決方法 |
|-----|-------------|---------|
| ローカル開発サーバーが起動しない | ポート競合 | `gulp serve --nobrowser --port=4321` を試す |
| Graph APIアクセスエラー | 権限不足 | SPFxマニフェストで正しい権限を設定、管理者の承認を得る |
| TypeScriptコンパイルエラー | 型定義の問題 | 該当するパッケージの@typesをインストール |
| WebPack関連のエラー | バンドル構成の問題 | `--no-cache`オプションで再ビルド、または`--clean`実行 |

#### Power Apps / Power Automate の問題

| 問題 | 考えられる原因 | 解決方法 |
|-----|-------------|---------|
| アプリの起動に時間がかかる | コンポーネントの過剰使用 | OnStartの最適化、Collectionの再利用、キャッシュの活用 |
| フローの実行が失敗する | 接続問題、式の誤り | トラブルシューティングツールで実行ログを確認、ステップごとに検証 |
| 式エラーが発生する | 文法ミス、データ型不一致 | 式を細分化して検証、データ型を明示的に変換 |
| 権限エラー | アクセス設定の問題 | データソースの接続を確認、共有権限を見直す |

### ロギングとモニタリング

#### SharePointアプリケーションのログ

SPFxアプリケーションにロギングを実装:

```typescript
// LogService.ts
export enum LogLevel {
  Debug = 0,
  Info = 1,
  Warning = 2,
  Error = 3
}

export class LogService {
  private static currentLevel: LogLevel = LogLevel.Info;
  private static isProduction: boolean = process.env.NODE_ENV === 'production';
  
  public static setLogLevel(level: LogLevel): void {
    this.currentLevel = level;
  }
  
  public static debug(message: string, data?: any): void {
    if (this.currentLevel <= LogLevel.Debug) {
      console.debug(`[DEBUG] ${message}`, data);
      // 開発環境のみDOM上に表示
      if (!this.isProduction) {
        this.addLogToDOM('debug', message);
      }
    }
  }
  
  public static info(message: string, data?: any): void {
    if (this.currentLevel <= LogLevel.Info) {
      console.info(`[INFO] ${message}`, data);
    }
  }
  
  public static warning(message: string, data?: any): void {
    if (this.currentLevel <= LogLevel.Warning) {
      console.warn(`[WARNING] ${message}`, data);
      // Application Insightsにログ送信（実装時）
      this.sendToApplicationInsights('warning', message, data);
    }
  }
  
  public static error(message: string, error?: any): void {
    if (this.currentLevel <= LogLevel.Error) {
      console.error(`[ERROR] ${message}`, error);
      // Application Insightsにエラー送信
      this.sendToApplicationInsights('error', message, error);
    }
  }
  
  private static sendToApplicationInsights(severity: string, message: string, data?: any): void {
    // Application Insightsへの送信処理（実装予定）
    // この部分は実際のAI連携コードで置き換える
  }
  
  private static addLogToDOM(level: string, message: string): void {
    if (typeof document !== 'undefined') {
      const logContainer = document.getElementById('dev-logs') || this.createLogContainer();
      const logEntry = document.createElement('div');
      logEntry.className = `log-entry log-${level}`;
      logEntry.innerText = `${new Date().toISOString()} [${level.toUpperCase()}] ${message}`;
      logContainer.appendChild(logEntry);
    }
  }
  
  private static createLogContainer(): HTMLElement {
    const container = document.createElement('div');
    container.id = 'dev-logs';
    container.style.position = 'fixed';
    container.style.bottom = '0';
    container.style.right = '0';
    container.style.width = '400px';
    container.style.maxHeight = '200px';
    container.style.overflow = 'auto';
    container.style.backgroundColor = 'rgba(0,0,0,0.8)';
    container.style.color = 'white';
    container.style.fontSize = '12px';
    container.style.padding = '5px';
    container.style.zIndex = '9999';
    
    document.body.appendChild(container);
    return container;
  }
}
```

#### Power Platform のモニタリング

Power Platformソリューションのモニタリング設定:

1. Power Platform CoE (Center of Excellence) キットのインストール
   - [Power Platform CoE Starter Kit](https://github.com/microsoft/coe-starter-kit)をダウンロード
   - 監視ダッシュボードをインポート

2. Power Platform Admin Centerの活用
   - [https://admin.powerplatform.microsoft.com/](https://admin.powerplatform.microsoft.com/)にアクセス
   - 「分析」セクションで使用状況とパフォーマンスを確認

3. カスタムテレメトリの実装
   - Power Automateフローでログ記録
   - Dataverseにログテーブルを作成
   - Power BIでログデータを可視化

## ベストプラクティス

### コーディング規約

#### SPFxとTypeScriptのコーディング規約

- **ファイル構造**: 機能ごとにディレクトリを分けて関連ファイルをグループ化
- **命名規則**:
  - コンポーネント: PascalCase (`NewsWebPart.tsx`)
  - インターフェース: 'I'プレフィックス + PascalCase (`INewsWebPartProps`)
  - 関数: camelCase (`fetchLatestNews`)
  - 定数: UPPER_SNAKE_CASE (`DEFAULT_FETCH_COUNT`)
- **コメント**: JSDocスタイルでクラス、メソッド、プロパティにコメントを追加
- **エラー処理**: 適切なエラーキャッチと意味のあるエラーメッセージ

ESLint設定例 (`.eslintrc.json`):

```json
{
  "extends": [
    "@microsoft/eslint-config-spfx/lib/profiles/react",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": {
    "@typescript-eslint/explicit-function-return-type": "warn",
    "@typescript-eslint/no-explicit-any": "warn",
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

#### Power Apps のベストプラクティス

- **命名規則**:
  - 画面: Screen + 機能名 (`ScreenHome`, `ScreenNewsList`)
  - コントロール: 種類 + 用途 (`btnSubmit`, `lblTitle`, `txtName`)
  - 変数: var + CamelCase (`varUserProfile`, `varSelectedItem`)
  - コレクション: col + CamelCase (`colNewsItems`, `colUserList`)
- **コメントと文書化**: 複雑なロジックには常にコメントを追加
- **パフォーマンスの最適化**:
  - OnStartで初期化が必要なもののみロード
  - コレクションの過剰使用を避ける
  - 巨大なギャラリーには段階的ロードを実装

### セキュリティのベストプラクティス

#### OWASP Top 10対応

1. **注入攻撃の防止**:
   - パラメータ化されたクエリを使用
   - ユーザー入力を適切にエスケープ

2. **クロスサイトスクリプティング (XSS) 対策**:
   - React/SPFxではデフォルトでエスケープ処理あり
   - Power Appsで直接HTMLを表示しない

3. **適切な認証と認可**:
   - 常にMicrosoft Entra IDを使用
   - 最小権限の原則に従う

4. **安全な通信**:
   - HTTPSのみ使用
   - 適切なCORS設定

#### データ保護

1. **個人情報の取り扱い**:
   - 必要最小限の個人情報のみ収集
   - 不要なデータは保存しない
   - アクセスログを記録

2. **データ暗号化**:
   - 保存データの暗号化（Dataverse, SharePoint）
   - 転送中のデータ暗号化（TLS 1.3）
   - 機密データの列レベル暗号化

3. **アクセス制御**:
   - ロールベースのアクセス制御
   - 条件付きアクセスポリシー
   - 特権アカウント管理

### パフォーマンス最適化

#### SPFxアプリケーションの最適化

1. **バンドルサイズの最適化**:
   - Tree Shakingの活用
   - コード分割とレイジーロード
   - 未使用のインポートを削除

2. **レンダリングパフォーマンス**:
   - メモ化（React.memo, useMemo, useCallback）
   - 仮想スクロールの実装
   - 状態更新の最適化

3. **ネットワークリクエストの最適化**:
   - バッチ処理
   - データキャッシュの活用
   - 必要最小限のデータのみ取得

#### Power Apps最適化

1. **フォーミュラの最適化**:
   - 複雑な計算を分解
   - OnVisibleでの不要な処理を避ける
   - グローバル変数の過剰使用を避ける

2. **データソース接続の最適化**:
   - 必要なデータのみ取得
   - キャッシングと委任処理を活用
   - バッチ操作を使用

3. **UI最適化**:
   - 画像の最適化
   - コンポーネントの再利用
   - コンテナの入れ子レベルを制限

## よくある質問

### 開発環境に関する質問

#### Q: 開発者テナントのライセンスが切れました。どうすればよいですか？
A: Microsoft 365 開発者プログラムサイトで、テナントの延長申請が可能です。開発者プログラムの規則に従って定期的にアクティビティを示すことで、継続的に利用できます。または、新しい開発者テナントを作成することも可能です。

#### Q: ローカル開発環境で認証エラーが発生します。
A: SPFxローカル開発環境では、ブラウザのストレージに古い認証トークンが残っていることがあります。ブラウザのキャッシュとCookieをクリアするか、シークレットモードで試してみてください。また、正しいテナントとサイトURLを使用していることを確認してください。

### Power Platform に関する質問

#### Q: Power Appsのパフォーマンスが遅いです。どうすれば改善できますか？
A: 以下の点を確認してください：
1. OnStartで不要な初期化をしていないか
2. 大きなコレクションを頻繁に更新していないか
3. 複雑なフォーミュラをシンプルにできないか
4. 画像サイズが最適化されているか
5. 委任可能なフィルター処理を使用しているか

#### Q: Dataverseとの接続が頻繁に失敗します。
A: 以下の対策を試してください：
1. 接続を削除して再作成する
2. 別のアカウントで試す（権限の問題の可能性）
3. エラーメッセージを詳細に確認して具体的な問題点を特定する
4. Power Platform管理センターでサービス正常性を確認する

### SharePoint関連の質問

#### Q: SPFxソリューションのデプロイ後に404エラーが発生します。
A: 以下の点を確認してください：
1. アプリカタログに正しくデプロイされているか
2. サイトにアプリが正しくインストールされているか
3. マニフェストのURLパスが正しいか
4. ページにWebパーツが追加されているか
5. ブラウザ開発者ツールでコンソールエラーの詳細を確認する

#### Q: SharePointリストにデータが表示されません。
A: 以下の点を確認してください：
1. リストのアクセス権限設定
2. SPFxコンポーネントが正しいコンテキストとアクセストークンを使用しているか
3. APIコールが正しく行われているか（開発者ツールのネットワークタブで確認）
4. リストの表示ビューと列の設定が適切か

### アーキテクチャに関する質問

#### Q: 段階的に拡張する場合、最初に注力すべき部分は？
A: まずは基本的なプラットフォーム基盤を整え、住民向けのコアサービスから実装することをお勧めします：
1. ユーザー認証・認可基盤
2. 住民情報の基本データモデル
3. 情報ポータル（ニュース、お知らせ）
4. 基本的な申請フォームとワークフロー

段階的に拡張するアプローチにより、早期からフィードバックを得ながら機能を追加できます。

#### Q: マイクロサービスアーキテクチャへの移行は検討すべきですか？
A: 初期フェーズでは、Microsoft 365とPower Platformの統合サービスを活用する方がコスト効率と開発速度の面で有利です。将来的にユーザー数や機能が大幅に増加した場合は、特定の高負荷コンポーネントをマイクロサービスとして切り出す戦略が考えられます。その際はAzure PaaSサービスの活用を検討してください。

### 運用・保守に関する質問

#### Q: システム更新はどのように行うべきですか？
A: 以下の更新戦略をお勧めします：
1. 小さな更新は定期的に（2-4週間ごと）実施
2. 大きな機能追加は四半期ごとにまとめて
3. 更新は必ずテスト環境で検証後に実施
4. 重要な更新は事前に住民にアナウンス
5. 更新履歴とドキュメントを常に最新に保つ

#### Q: データバックアップはどのように計画すべきですか？
A: 以下のバックアップ戦略を実装してください：
1. Dataverse: 日次自動バックアップ（標準機能）+ 手動バックアップ（重要変更前）
2. SharePoint: 週次バックアップ + サイトコレクションのバックアップ
3. 重要構成設定: リポジトリでバージョン管理
4. 一定期間（最低3ヶ月）のバックアップを保持
5. 定期的なリストア訓練の実施

#### Q: 障害発生時の対応手順はどうあるべきですか？
A: 以下の障害対応フローを確立してください：
1. インシデント検知：監視アラートまたはユーザー報告
2. 初期評価：影響範囲と重要度の判断
3. 対応チームの編成：必要な専門家の招集
4. コミュニケーション：関係者への適切な情報共有
5. 解決策の実施：問題の修正
6. 検証：解決の確認
7. 事後分析：根本原因の特定と再発防止策の検討

### 権限とセキュリティに関する質問

#### Q: 最小特権の原則をどう実装すればよいですか？
A: 以下のアプローチを採用してください：
1. 役割ベースのアクセス制御（RBAC）を実装
2. 詳細な権限マトリックスを作成（ユーザー役割×機能/データ）
3. 既定では読み取り専用権限を付与
4. 特権アカウントの使用を監査・記録
5. 定期的な権限レビューと不要な権限の削除

#### Q: 個人情報保護法への対応方法は？
A: 以下の対策を実装してください：
1. 個人データマッピングの実施（どの情報がどこに保存されているか）
2. 個人情報の利用目的の明確化と同意取得
3. データアクセスの厳格な制限と監査
4. データ保持ポリシーの実装（不要データの自動削除）
5. ユーザーからの情報開示・訂正・削除要求への対応手順の確立
6. 定期的なプライバシーアセスメントの実施

## テクニカルノート

### コード例の追加資料

#### SPFx – 多言語対応の実装

```typescript
// src/webparts/newsWebPart/loc/mystrings.d.ts
declare interface INewsWebPartStrings {
  PropertyPaneDescription: string;
  WebPartTitle: string;
  LoadingMessage: string;
  ErrorMessage: string;
  ReadMoreLabel: string;
  NoNewsMessage: string;
}

declare module 'NewsWebPartStrings' {
  const strings: INewsWebPartStrings;
  export = strings;
}
```

```typescript
// src/webparts/newsWebPart/loc/en-us.js
define([], function() {
  return {
    "PropertyPaneDescription": "Configure the News Web Part",
    "WebPartTitle": "News Web Part",
    "LoadingMessage": "Loading news...",
    "ErrorMessage": "An error occurred while loading news",
    "ReadMoreLabel": "Read more",
    "NoNewsMessage": "No news available"
  }
});
```

```typescript
// src/webparts/newsWebPart/loc/ja-jp.js
define([], function() {
  return {
    "PropertyPaneDescription": "ニュースWebパーツの設定",
    "WebPartTitle": "ニュースWebパーツ",
    "LoadingMessage": "ニュースを読み込み中...",
    "ErrorMessage": "ニュースの読み込み中にエラーが発生しました",
    "ReadMoreLabel": "詳細を見る",
    "NoNewsMessage": "ニュースがありません"
  }
});
```

```typescript
// NewsWebPart.tsx
import * as React from 'react';
import * as strings from 'NewsWebPartStrings';

// 文字列リソースの使用例
public render(): React.ReactElement<INewsWebPartProps> {
  const { loading, error, newsItems } = this.state;
  
  if (loading) {
    return <div>{strings.LoadingMessage}</div>;
  }
  
  if (error) {
    return <div className="error">{strings.ErrorMessage}</div>;
  }
  
  if (newsItems.length === 0) {
    return <div>{strings.NoNewsMessage}</div>;
  }
  
  return (
    <div className="news-webpart">
      <h2 className="news-title">{this.props.title || strings.WebPartTitle}</h2>
      {/* ... */}
      <a href={item.url}>{strings.ReadMoreLabel}</a>
    </div>
  );
}
```

#### Power Automate – 複雑な承認ワークフロー

```yaml
# 高度な申請承認フロー（擬似コード）
triggers:
  - type: PowerApps
    inputs:
      schema:
        type: object
        properties:
          applicationId: { type: string }
          applicationTitle: { type: string }
          applicationDescription: { type: string }
          applicantId: { type: string }
          applicantEmail: { type: string }
          category: { type: string }
          priority: { type: integer }
          attachments: { type: array, items: { type: object } }

actions:
  # 申請データの取得と検証
  - name: "Get_Application_Details"
    type: "Dataverse.GetRecord"
    inputs:
      entityName: "cr7e2_applications"
      recordId: "@triggerBody()['applicationId']"
  
  # カテゴリに基づく承認者の決定
  - name: "Determine_Approvers"
    type: "Switch"
    inputs:
      expression: "@triggerBody()['category']"
      cases:
        - case: "Facilities"
          actions:
            - name: "Get_Facilities_Approvers"
              type: "Dataverse.ListRecords"
              inputs:
                entityName: "cr7e2_approvers"
                filter: "cr7e2_category eq 'Facilities' and cr7e2_isactive eq true"
        - case: "IT"
          actions:
            - name: "Get_IT_Approvers"
              # ...省略...
        - default:
          actions:
            - name: "Get_Default_Approvers"
              # ...省略...
  
  # 承認プロセスの作成
  - name: "Create_Approval_Task"
    type: "Approvals.CreateApproval"
    inputs:
      approvalType: "Sequential"
      title: "申請承認リクエスト: @{triggerBody()['applicationTitle']}"
      details: "申請者: @{triggerBody()['applicantEmail']}\n\n@{triggerBody()['applicationDescription']}"
      itemsToReview: "@outputs('Get_Application_Details')?['body']"
      approvers: "@body('Determine_Approvers')?['value']"
      notificationSettings:
        notificationContentType: "textAndLink"
        notificationMessage: "新しい申請が承認待ちです"
        includeLink: true
  
  # 承認結果の待機
  - name: "Wait_For_Approval"
    type: "Approvals.WaitForApproval"
    inputs:
      approvalCorrelationId: "@outputs('Create_Approval_Task')['approval']['id']"
  
  # 承認結果に基づく条件分岐
  - name: "Check_Approval_Result"
    type: "If"
    inputs:
      expression: "@equals(outputs('Wait_For_Approval')['approval']['status'], 'Approved')"
      actions:
        # 承認された場合の処理
        - name: "Update_Application_Approved"
          type: "Dataverse.UpdateRecord"
          inputs:
            entityName: "cr7e2_applications"
            recordId: "@triggerBody()['applicationId']"
            item:
              cr7e2_status: 2 # 承認済み
              cr7e2_approveddate: "@utcNow()"
              cr7e2_approvalnotes: "@outputs('Wait_For_Approval')['approval']['comments']"
        
        - name: "Send_Approval_Notification"
          type: "Office365.SendEmail"
          inputs:
            to: "@triggerBody()['applicantEmail']"
            subject: "申請が承認されました: @{triggerBody()['applicationTitle']}"
            body: "<p>申請が承認されました。</p><p>承認者コメント: @{outputs('Wait_For_Approval')['approval']['comments']}</p>"
      else:
        # 却下された場合の処理
        - name: "Update_Application_Rejected"
          type: "Dataverse.UpdateRecord"
          inputs:
            entityName: "cr7e2_applications"
            recordId: "@triggerBody()['applicationId']"
            item:
              cr7e2_status: 3 # 却下
              cr7e2_rejectionreason: "@outputs('Wait_For_Approval')['approval']['comments']"
        
        - name: "Send_Rejection_Notification"
          type: "Office365.SendEmail"
          inputs:
            to: "@triggerBody()['applicantEmail']"
            subject: "申請が却下されました: @{triggerBody()['applicationTitle']}"
            body: "<p>申請が却下されました。</p><p>却下理由: @{outputs('Wait_For_Approval')['approval']['comments']}</p>"
  
  # 申請履歴の記録
  - name: "Log_Application_History"
    type: "Dataverse.CreateRecord"
    inputs:
      entityName: "cr7e2_applicationhistory"
      item:
        cr7e2_application: "@triggerBody()['applicationId']"
        cr7e2_action: "@if(equals(outputs('Wait_For_Approval')['approval']['status'], 'Approved'), '承認', '却下')"
        cr7e2_actor: "@outputs('Wait_For_Approval')['approval']['approver']"
        cr7e2_comments: "@outputs('Wait_For_Approval')['approval']['comments']"
        cr7e2_timestamp: "@utcNow()"
```

#### Azure Functions - Dataverse連携コード

```javascript
// Azure Function for processing resident data
const { DefaultAzureCredential } = require("@azure/identity");
const { DynamicsWebApi } = require("dynamics-web-api");

module.exports = async function (context, req) {
    try {
        // 認証情報の取得
        const credential = new DefaultAzureCredential();
        const accessToken = await credential.getToken("https://orgname.crm.dynamics.com/.default");
        
        // DynamicsWebApiの設定
        const dynamicsWebApi = new DynamicsWebApi({
            webApiUrl: "https://orgname.crm.dynamics.com/api/data/v9.2/",
            onTokenRefresh: async (callback) => {
                try {
                    const tokenResponse = await credential.getToken("https://orgname.crm.dynamics.com/.default");
                    callback(tokenResponse.token);
                } catch (error) {
                    context.log.error("Error refreshing token:", error);
                    throw error;
                }
            }
        });
        
        // 住民データの取得
        const fetchXml = `
            <fetch>
                <entity name="cr7e2_resident">
                    <attribute name="cr7e2_residentid" />
                    <attribute name="cr7e2_name" />
                    <attribute name="cr7e2_email" />
                    <attribute name="cr7e2_age" />
                    <attribute name="cr7e2_registrationdate" />
                    <attribute name="cr7e2_category" />
                    <filter>
                        <condition attribute="statecode" operator="eq" value="0" />
                        <condition attribute="cr7e2_registrationdate" operator="last-x-months" value="6" />
                    </filter>
                </entity>
            </fetch>
        `;
        
        const residents = await dynamicsWebApi.executeFetchXml("cr7e2_residents", fetchXml);
        
        // 住民データの分析
        const totalResidents = residents.value.length;
        
        // 年齢層分析
        const ageGroups = {
            under18: 0,
            age18to30: 0,
            age31to50: 0,
            age51to65: 0,
            over65: 0
        };
        
        residents.value.forEach(resident => {
            const age = resident.cr7e2_age;
            if (age < 18) ageGroups.under18++;
            else if (age <= 30) ageGroups.age18to30++;
            else if (age <= 50) ageGroups.age31to50++;
            else if (age <= 65) ageGroups.age51to65++;
            else ageGroups.over65++;
        });
        
        // カテゴリー分析
        const categoryAnalysis = {};
        residents.value.forEach(resident => {
            const category = resident.cr7e2_category || "未分類";
            categoryAnalysis[category] = (categoryAnalysis[category] || 0) + 1;
        });
        
        // 登録日分析（月別）
        const registrationByMonth = {};
        residents.value.forEach(resident => {
            const date = new Date(resident.cr7e2_registrationdate);
            const yearMonth = `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(2, '0')}`;
            registrationByMonth[yearMonth] = (registrationByMonth[yearMonth] || 0) + 1;
        });
        
        // 分析結果をDataverseに保存
        const analyticsData = {
            cr7e2_name: `住民分析 ${new Date().toISOString().split('T')[0]}`,
            cr7e2_totalresidents: totalResidents,
            cr7e2_ageanalysis: JSON.stringify(ageGroups),
            cr7e2_categoryanalysis: JSON.stringify(categoryAnalysis),
            cr7e2_registrationanalysis: JSON.stringify(registrationByMonth),
            cr7e2_generationdate: new Date().toISOString()
        };
        
        await dynamicsWebApi.create(analyticsData, "cr7e2_residentanalytics");
        
        // Power BIデータセットの更新トリガー
        // ...省略...
        
        context.res = {
            status: 200,
            body: {
                message: "住民データ分析が完了しました",
                totalResidents,
                ageGroups,
                categoryAnalysis,
                registrationByMonth
            }
        };
    } catch (error) {
        context.log.error("Function execution failed:", error);
        
        context.res = {
            status: 500,
            body: {
                error: "住民データの処理中にエラーが発生しました",
                details: error.message
            }
        };
    }
};
```

### クォータとリソース制限

#### Microsoft 365およびPower Platformの制限

以下の制限値を考慮してアプリケーションを設計してください：

| サービス | リソース | 制限 |
|---------|---------|------|
| SharePoint Online | リストアイテム | 30,000,000アイテム/リスト |
| SharePoint Online | ファイルサイズ | 250GB/ファイル |
| SharePoint Online | API呼び出し | 600リクエスト/分/アプリ |
| Power Apps | アプリサイズ | 50MB/アプリ |
| Power Apps | データ行 | 50,000行/テーブル（標準環境） |
| Power Apps | アクション | 2,000/日/ユーザー |
| Power Automate | 実行回数 | 5,000/月/フロー（標準) |
| Power Automate | アクション | 500/24時間/接続 |
| Dataverse | ストレージ容量 | 初期10GB + ユーザーあたり1GB |
| Dataverse | ファイルサイズ | 128MB/添付ファイル |

これらの制限を超える場合は、以下の対策を検討してください：
- 追加ライセンスの購入
- データアーカイブ戦略の実装
- 分散型設計アプローチの採用
- バッチ処理とキュー機構の実装

### 追加リソースとリンク

開発の参考になる公式ドキュメントとリソース：

1. **SharePoint Framework (SPFx)**
   - [SPFx開発者ドキュメント](https://docs.microsoft.com/ja-jp/sharepoint/dev/spfx/sharepoint-framework-overview)
   - [PnPコミュニティサンプル](https://pnp.github.io/sp-dev-fx-webparts/)

2. **Power Platform**
   - [Power Apps開発者ドキュメント](https://docs.microsoft.com/ja-jp/powerapps/developer/common-data-service/overview)
   - [Power Automateドキュメント](https://docs.microsoft.com/ja-jp/power-automate/)
   - [Microsoft Power Platform チームブログ](https://powerapps.microsoft.com/ja-jp/blog/)

3. **Microsoft Graph API**
   - [Microsoft Graphドキュメント](https://docs.microsoft.com/ja-jp/graph/overview)
   - [Graph Explorerツール](https://developer.microsoft.com/ja-jp/graph/graph-explorer)

4. **Azure**
   - [Azure Functionsドキュメント](https://docs.microsoft.com/ja-jp/azure/azure-functions/)
   - [Azure開発者ガイド](https://docs.microsoft.com/ja-jp/azure/guides/developer/azure-developer-guide)

5. **セキュリティとコンプライアンス**
   - [Microsoft 365セキュリティ](https://docs.microsoft.com/ja-jp/microsoft-365/security/)
   - [Microsoft Purviewドキュメント](https://docs.microsoft.com/ja-jp/microsoft-365/compliance/)

---

このガイドは開発チームの成長とともに継続的に更新されます。ご質問や追加情報のリクエストがあれば、Discussionsフォーラムでお気軽にご連絡ください。
