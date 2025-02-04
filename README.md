------------------------------------------------------------------------------------------------------------------
📌 プロジェクト README

🏗 プロジェクト概要
このプロジェクトは、ユーザー認証機能と学習ログ（Techlog）投稿機能を備えたRuby on Rails 7 ベースのWebアプリケーションです。
Devise、Tailwind CSS、RSpec、Rubocopなどを活用し、セキュリティ、保守性、拡張性を考慮した設計を行いました。

🚀 技術的な強み：
• 最新のRails 7のHotwire & Turboを活用し、"動的なページ遷移を実現
• Devise + OmniAuthを活用し、拡張性のある認証システムを構築
• Tailwind CSSを用いたレスポンシブなデザイン
• RSpec + Capybara による自動テストの導入
• Rubocopを活用し、コード品質を統一＆向上

------------------------------------------------------------------------------------------------------------------

📌 プロジェクトのセットアップ手順

① プロジェクトのディレクトリ構成と環境設定
$ mkdir techlog-app && cd techlog-app
• バージョン管理: rbenvを使用し、Ruby・Railsのバージョンを統一
• 環境変数管理: .envを活用し、APIキーやDB設定を安全に管理

② Ruby & Rails のインストール
$ rbenv install 3.0.4
$ rbenv local 3.0.4
$ gem install bundler
$ gem install rails -v 7.0.x
$ rails new . --database=postgresql --css=tailwind
• PostgreSQLを選択した理由: スケーラビリティとパフォーマンスを考慮
• Tailwind CSSを選択した理由: 可読性の高いクラス命名 & 最適化されたデザインフレームワーク

③ データベース設定と初期化
$ rails db:create
• config/database.yml の設定
    • 本番環境: DATABASE_URLを使用し、環境変数経由で設定
    • 開発・テスト環境: config/database.yml に明示的に設定

④ ユーザー認証（Devise & OmniAuth）
$ bundle add devise
$ rails generate devise:install
$ rails generate devise User
$ rails db:migrate
• Deviseを使用する理由:
    • 強力なセキュリティ機能（ハッシュ化されたパスワード保存、自動セッション管理）
    • さまざまな認証方式に対応（OmniAuthと組み合わせ可能）
    • シンプルな設定で強力な認証機能を実装可能
• GitHubログインを追加
    $ bundle add omniauth-github
    $ rails generate devise:controllers users
    • omniauth_callbacks_controller を用いたカスタム実装が必要

⑤ 学習ログ（Techlog）機能の実装
$ rails generate scaffold Techlog title:string content:text user:references
$ rails db:migrate
• Techlog モデル設計
    • title: 学習ログのタイトル（必須）
    • content: 学習内容の詳細
    • user: 投稿者（Userモデルと関連付け）

• 🔍 ビジネスロジック
    • 未ログインユーザーの制限: before_action :authenticate_user! を追加
    • 自分の投稿のみ編集可能: before_action :authorize_user!, only: [:edit, :update, :destroy]
    • N+1問題対策: includes(:user) を活用し、preload や eager_load も適宜活用、Bulletで検出

⑥ Hotwire & Turboを活用したインタラクティブなUI
$ rails turbo:install
• 技術選定の理由
    • Hotwire + Turboを使用することで、JavaScriptの記述を最小限に抑えつつ、動的なUIを構築
    • 即時性のあるUXを提供
    • フロントエンド開発の負担を軽減し、Rails中心の開発を維持
• 適用例: 学習ログの投稿時に非同期でページ更新
    <%= form_with(model: @techlog, data: { turbo: true }) do |f| %>
      <%= f.text_field :title %>
      <%= f.text_area :content %>
      <%= f.submit "投稿する" %>
    <% end %>

⑦ 自動テスト（RSpec & Capybara）
$ bundle add rspec-rails capybara
$ rails generate rspec:install
• ユニットテスト: RSpec を使用し、モデル・コントローラのテストを作成
• 統合テスト: Capybara を活用し、E2Eテストを自動化
• CI/CD統合: GitHub Actions でテストを自動実行
• Techlogモデルのテスト例
    require 'rails_helper'

    RSpec.describe Techlog, type: :model do
      it "タイトルがない場合、無効であること" do
        techlog = Techlog.new(content: "内容")
        expect(techlog).not_to be_valid
      end
    end

⑧ コード品質管理（Rubocop 設定）
$ bundle add rubocop rubocop-rails
$ rubocop --init
• 一貫したコーディングスタイルの維持
• 自動コード修正（rubocop -A）によるコード品質向上
• GitHub Actions で Rubocopを実行し、PR作成時にスタイルチェックを自動化

------------------------------------------------------------------------------------------------------------------

📌 技術的な課題と解決策

1. N+1問題の解決
• includes(:user) を使用し、各Techlogの取得時に余分なクエリを実行しないよう最適化

2. セキュリティ強化
• Devise の confirmable 機能を有効化し、メール認証を必須化
• CSRF対策として protect_from_forgery を適用

3. パフォーマンス最適化
• 頻繁に取得するデータを Rails.cache でキャッシュの適用範囲と無効化ポリシーを考慮しつつ使用
• turbo_stream を活用し、サーバー負荷を軽減

🚀 デプロイと運用
• Docker コンテナ化（本番環境に応じて調整可能）
• Heroku デプロイ
    $ git push heroku main
    $ heroku run rails db:migrate
• AWS + Nginx + Puma構成も可能（スケーラビリティを考慮）
