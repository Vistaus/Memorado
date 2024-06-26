message('compiling resources')

blueprints = custom_target('blueprints',
  input: files(
    'ui/window.blp',
    'ui/welcome.blp',
    'ui/list_view.blp',
    'ui/deck_view.blp',
    'ui/card_view.blp',
    'ui/card_new_view.blp',
    'ui/deck_row.blp',
    'ui/card_row.blp',
    'ui/card_edit.blp',
  ),
  output: '.',
  command: [find_program('blueprint-compiler'), 'batch-compile', '@OUTPUT@', '@CURRENT_SOURCE_DIR@', '@INPUT@'],
)

about_conf = configuration_data()
about_conf.set('PACKAGE_URL', package_url)
about_conf.set('ISSUE_URL', issue_url)
about_conf.set('CONTRIBUTORS', contributors)
about_conf.set('DESIGNERS', designers)
about_conf.set('ARTISTS', artists)
about_conf.set('APPID', application_id)
about_conf.set('VERSION', version)
about_conf.set('COPYRIGHT', copyright)

gnome.compile_resources(
  'resources',
  'resources.gresource.xml',
  gresource_bundle: true,
  source_dir: meson.current_build_dir(),
  install_dir: DATA_DIR,
  install: true,
  dependencies: [
    configure_file (
      input: 'about_dialog.ui.in',
      output: '@BASENAME@',
      configuration: about_conf
    ),
    blueprints
  ]
)

gschema_conf = configuration_data()
gschema_conf.set('APP_ID', application_id)
gschema_conf.set('GETTEXT_PACKAGE', gettext_package)
configure_file(
    input: '@0@.gschema.xml.in'.format(project_id),
    output: '@0@.gschema.xml'.format(application_id),
    configuration: gschema_conf,
    install: true,
    install_dir: 'share/glib-2.0/schemas'
)

desktop_conf = configuration_data()
desktop_conf.set('ICON', application_id)
desktop = i18n.merge_file(
  input: configure_file(
    input: files('@0@.desktop.in.in'.format(project_id)),
    output: '@0@.desktop.in'.format(project_id),
    configuration: desktop_conf
  ),
  output: '@0@.desktop'.format(application_id),
  po_dir: join_paths(meson.project_source_root(), 'po'),
  type: 'desktop',
  install: true,
  install_dir: join_paths(get_option('prefix'), get_option('datadir'), 'applications')
)

# Validating the desktop file
desktop_file_validate = find_program('desktop-file-validate', required:false)
if desktop_file_validate.found()
  test (
    'Validate desktop file',
    desktop_file_validate,
    args: join_paths(meson.current_build_dir (), application_id + '.desktop')
  )
endif

metainfo_conf = configuration_data()
metainfo_conf.set('APPID', application_id)
metainfo_conf.set('GETTEXT_PACKAGE', meson.project_name())
metainfo_conf.set('NAME', project_name)
metainfo_conf.set('PACKAGE_URL', package_url)
metainfo_conf.set('ISSUE_URL', issue_url)
metainfo = i18n.merge_file(
  input: configure_file(
    input: files('@0@.metainfo.xml.in.in'.format(project_id)),
    output: '@0@.metainfo.xml.in'.format(project_id),
    configuration: metainfo_conf
  ),
  output: '@0@.metainfo.xml'.format(project_id),
  install: true,
  install_dir: join_paths(join_paths('share'), 'metainfo'),
  po_dir: join_paths(meson.project_source_root(), 'po')
)

# Validating the metainfo file
appstreamcli = find_program('appstream-util', required: false)
if appstreamcli.found()
  test (
    'Validate metainfo file',
    appstreamcli,
    args: ['validate-relax', '--nonet', join_paths(meson.current_build_dir (), application_id + '.metainfo.xml')]
  )
endif

# Validating schemas
compile_schemas = find_program('glib-compile-schemas', required: false)
if compile_schemas.found()
  test('Validate schema file', compile_schemas,
    args: ['--strict', '--dry-run', meson.current_source_dir()]
  )
endif

subdir('icons')
