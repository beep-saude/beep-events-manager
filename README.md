# Beep::EventsManager



## Installation

```ruby
gem "beep-events_manager", :github => "beep-saude/beep-events-manager", :tag => "0.1.3"
```
Criando o database
```sql
CREATE DATABASE beep_events
CREATE TABLE events (id SERIAL, object_id integer, date timestamp, event_name varchar(100), event_data json, object_domain varchar(30), object_type varchar(30))
```
crie o arquivo de configuração no initializer event_manager_config.rb
```ruby
config = Beep::EventsManager::Config.instance
config.object_domain = "app-name"
config.database_config = { dbname: "beep_events", user: "", host: "localhost", sslmode: 'disable' }
```

## Usage

### Criar um evento
```ruby
  event = Beep::EventsManager::Event.new(id: 0, object_id: 1, date: Time.zone.now, event_name: "seu-evento", event_data: { foo: "bar"}, object_domain: "app-name", object_type: "Object")
  event_store = Beep::EventsManager::EventStore.new
  event_store.store( event )
```

### Usar Publisher/Subscriber

```ruby
  event = Beep::EventsManager::Event.new(id: 0, object_id: 1, date: Time.zone.now, event_name: "seu-evento", event_data: { foo: "bar"}, object_domain: "app-name", object_type:

  handlers = [ MyHandler.new, MyEventStoreHandler.new, EventStoreHandler.build ]
  publisher = Beep::EventsManager::Publisher.new
  publisher.subscribe( "seu-evento", handlers)

  publisher.publish(event)
```

* Para salvar na tabela beep_events, usar o EventStoreHandler.build
