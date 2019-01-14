= Redmine
## Redmine Agile�C���X�g�[��

%REDMINE_APP%/plugins�ɃR�s�[

~~~
# �ȉ��̂悤�ȍ\��
%REDMINE_APP%/plugins/redmine_agile/init.rb

# bundle install
bundle install --without development test

# migrate
bundle exec rake redmine:plugins NAME=redmine_agile RAILS_ENV=production
~~~

## �e��R���g���[��

### �`�P�b�g�ꗗ

* controller
    * issues_controller.rb
* view
    * �ꗗ����
        * _list.html.erb
        * 30�s�ڕt�߂���`�P�b�g�ꗗ

### Agile

* controller
    * agile_boards_controller.rb
* view
    * �J�[�h����
        * _issue_card.html.erb
        * �J�[�h�ꖇ���Ƃ�erb

## ��{���̎擾

~~~
# id
issue.id

# �`�P�b�g����
issue.subject

# ����
issue.due_date

# �D��x�i�����j
issue.priority

# �J�X�^���t�B�[���h�͌�q

~~~

## �J�X�^���t�B�[���h�ւ̃A�N�Z�X

### �����[�V����

~~~
# �J�X�^���t�B�[���h��
issue.available_custom_fields[0].name

# �l
issue.available_custom_fields[0].custom_values[0].value
~~~

## Agile�̗�̃��[��

�f�t�H���g�\�������̂́A�v���W�F�N�g�Ŏg�p����`�P�b�g�X�e�[�^�X���u�I���v�����ɂȂ��Ă��Ȃ�����

* ��ʃI�v�V�����̃`�F�b�N�{�b�N�X
    * %REDMINE_APP%/plugins/redmine_agile/app/helpers/agile_boards_helper.rb
    * 67�s��

~~~
  def render_board_fields_status(query)
    available_statuses = Redmine::VERSION.to_s >= '3.4' && @project ? @project.rolled_up_statuses : IssueStatus.sorted
    # �`�P�b�g���I�������ɂȂ��Ă��Ȃ����̂�ID���X�g�i���ꂪ�f�t�H���g�ŕ\��������ƂȂ�j
    current_statuses = query.options[:f_status] || IssueStatus.where(:is_closed => false).pluck(:id).map(&:to_s)
    wp = query.options[:wp] || {}
    status_tags = available_statuses.map do |status|
      # �I���������o���Ȃ炱����ID��ǉ�����
      checked = current_statuses.include?(status.id.to_s) || status.to_s == "����"
      label_tag('', check_box_tag('f_status[]', status.id, checked) + status.to_s, :class => 'floating')

      # label_tag('', check_box_tag('f_status[]', status.id, current_statuses.include?(status.id.to_s)
      # ) + status.to_s, :class => 'floating')
    end.join(' ').html_safe
    hidden_field_tag('f[]', 'status_id').html_safe +
      hidden_field_tag('op[status_id]', "=").html_safe +
      status_tags
  end
~~~

* �����\����
    * %REDMINE_APP%/plugins/redmine_agile/app/controllers/agile_board_controller.rb
    * 56�s��
~~~
status = IssueStatus.where(name: "�I��")
@board_columns << status.first if status.present?
~~~

## Agile�̃T�C�h�o�[�ɂ���v���O�C���̏�������

* %REDMINE_APP%/plugins/redmine_agile/app/views/agile_boards/_index.html.erb

73�s�ڕt�߂̈ȉ����R�����g

~~~
<%= render :partial => 'upgrade_to_pro' %>
~~~

## �`�P�b�g�^�u�́uAgile�v��ς�����

* %REDMINE_APP%/plugins/redmine_agile/config/locale/en.yml

### vi�ŕ����R�[�h�ς���

~~~
set fenc=UTF8
~~~

