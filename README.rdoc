= Redmine
## Redmine Agileインストール

%REDMINE_APP%/pluginsにコピー

~~~
# 以下のような構成
%REDMINE_APP%/plugins/redmine_agile/init.rb

# bundle install
bundle install --without development test

# migrate
bundle exec rake redmine:plugins NAME=redmine_agile RAILS_ENV=production
~~~

## 各種コントローラ

### チケット一覧

* controller
    * issues_controller.rb
* view
    * 一覧部分
        * _list.html.erb
        * 30行目付近からチケット一覧

### Agile

* controller
    * agile_boards_controller.rb
* view
    * カード部分
        * _issue_card.html.erb
        * カード一枚ごとのerb

## 基本情報の取得

~~~
# id
issue.id

# チケット件名
issue.subject

# 期日
issue.due_date

# 優先度（文字）
issue.priority

# カスタムフィールドは後述

~~~

## カスタムフィールドへのアクセス

### リレーション

~~~
# カスタムフィールド名
issue.available_custom_fields[0].name

# 値
issue.available_custom_fields[0].custom_values[0].value
~~~

## Agileの列のルール

デフォルト表示されるのは、プロジェクトで使用するチケットステータスが「終了」扱いになっていないもの

* 画面オプションのチェックボックス
    * %REDMINE_APP%/plugins/redmine_agile/app/helpers/agile_boards_helper.rb
    * 67行目

~~~
  def render_board_fields_status(query)
    available_statuses = Redmine::VERSION.to_s >= '3.4' && @project ? @project.rolled_up_statuses : IssueStatus.sorted
    # チケットが終了扱いになっていないもののIDリスト（これがデフォルトで表示される列となる）
    current_statuses = query.options[:f_status] || IssueStatus.where(:is_closed => false).pluck(:id).map(&:to_s)
    wp = query.options[:wp] || {}
    status_tags = available_statuses.map do |status|
      # 終了扱いを出すならここでIDを追加する
      checked = current_statuses.include?(status.id.to_s) || status.to_s == "解決"
      label_tag('', check_box_tag('f_status[]', status.id, checked) + status.to_s, :class => 'floating')

      # label_tag('', check_box_tag('f_status[]', status.id, current_statuses.include?(status.id.to_s)
      # ) + status.to_s, :class => 'floating')
    end.join(' ').html_safe
    hidden_field_tag('f[]', 'status_id').html_safe +
      hidden_field_tag('op[status_id]', "=").html_safe +
      status_tags
  end
~~~

* 初期表示時
    * %REDMINE_APP%/plugins/redmine_agile/app/controllers/agile_board_controller.rb
    * 56行目
~~~
status = IssueStatus.where(name: "終了")
@board_columns << status.first if status.present?
~~~

## Agileのサイドバーにあるプラグインの情報を消す

* %REDMINE_APP%/plugins/redmine_agile/app/views/agile_boards/_index.html.erb

73行目付近の以下をコメント

~~~
<%= render :partial => 'upgrade_to_pro' %>
~~~

## チケットタブの「Agile」を変えたい

* %REDMINE_APP%/plugins/redmine_agile/config/locale/en.yml

### viで文字コード変える

~~~
set fenc=UTF8
~~~

