# SproutCore Extensions

This repository contains extensions to the SproutCore framework.  To use an
extension within your project, clone this repository into the frameworks
directory and include the desired extensions in your Buildfile.

For example,

    config :my_app, :required => [:sproutcore, :'extensions/multiplexed_data_source']

## Multiplexed Data Source Extension

* Path: 'extensions/multiplexed_data_source'
* Requires: 'sproutcore/datastore'
* Original Author: Tim Evans

A multiplexed data source will forward data to a number of registered
SC.DataSourceDelegates that accept the SC.Record type provided.
This should be used when you have a single channel that has a multitude of
record types that are non-trivial to transform into data hashes suitable for
the store.

For example,

    MyApp.dataSource = SC.MultiplexedDataSource.create({
      delegates: 'presence chat muc roster vcard'.w(),

      presence: MyApp.PresenceDataSourceDelegate,
      chat: MyApp.MessageDataSourceDelegate.extend({ type: 'chat' }),
      muc: MyApp.MultiUserChatSourceDelegate,
      roster: MyApp.RosterItemDataSourceDelegate,
      vcard: MyApp.VCardTempDataSourceDelegate
    });

    MyApp.store.set('dataSource', MyApp.dataSource);

The order of the delegates is irrelevant. Queries and CRUD actions will
be forwarded to the apropriated delegate(s).

Alternatively, you can use a more jQuery-like API for defining your data
sources:

    MyApp.dataSource = SC.MultiplexedDataSource.create()
      .from(MyApp.PresenceDataSourceDelegate)
      .from(MyApp.MessageDataSourceDelegate.extend({ type: 'chat' }))
      .from(MyApp.MultiUserChatSourceDelegate)
      .from(MyApp.RosterItemDataSourceDelegate)
      .from(MyApp.VCardTempDataSourceDelegate)

    MyApp.store.set('dataSource', MyApp.dataSource);

A similar API can be used before creation time that allows SC.DataSourceDelegates
to be wired to their parent SC.DataSource via the `plugin` method.

The child delegates have direct access to the parent SC.MultiplexedDataSource,
which acts like a hub where all common functions and properties should be
placed.
