# Beep::EventsManager



## Installation

```ruby
gem "beep-events_manager", :github => "beep-saude/beep-events-manager", :tag => "1.0.1"
```
Criando o database
```sql
CREATE DATABASE beep_events;
CREATE TABLE events (id bigserial primary key, object_id integer, date timestamp, event_name varchar(100), event_data json, object_domain varchar(30), object_type varchar(30));
ALTER TABLE events ALTER COLUMN object_domain TYPE varchar(100);
ALTER TABLE events ALTER COLUMN object_type TYPE varchar(100);
CREATE INDEX CONCURRENTLY events_object_id_and_object_type_idx ON events(object_id, object_type);
```
crie o arquivo de configuração no initializer event_manager_config.rb
```ruby
config = Beep::EventsManager::Config.instance
config.object_domain = "app-name"
config.configure_with({ dbname: "beep_events", user: "", host: "localhost", sslmode: 'disable'})
```

## Usage

### Publicar um evento

```ruby
  events_service = Beep::EventsManager::EventsService.new
  events_service.publish( event_name: "seu-evento", object_id: 1, object_type: "Call", object_data: { foo: "bar" } )
```

### Listar os eventos de um objeto

```ruby
  events_service = Beep::EventsManager::EventsService.new
  events_service.list_events( object_id: 1, object_type: "Call", object_domain: "app-name" )
```

## Corrigir problema de tabela de eventos com id sem pk

Remover ids duplicados
```sql
DELETE FROM events
  WHERE ctid = ANY(ARRAY(SELECT ctid
                  FROM (SELECT row_number() OVER (PARTITION BY id), ctid
                           FROM events) x
                 WHERE x.row_number > 1));
```

Alterar a tabela para adicionar primary key
```sql
ALTER TABLE events ADD PRIMARY KEY (id);
```
