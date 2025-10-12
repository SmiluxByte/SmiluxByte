# 🔄 ReplicationController - Custom Replication System

## 🔍 Übersicht
Der ReplicationController ist ein custom Replikationssystem, das auf dem ViRus Framework basiert und für die Synchronisation von Spielerdaten zwischen Server und Client verwendet wird.

## 📁 Wichtige Dateien
- **Client**: `ReplicatedStorage/Modules/Libraries/ReplicaController`
- **Server**: `ServerStorage/Modules/Libraries/ReplicaService`

## ⚙️ Hauptfunktionen

### 1. 🖥️ ReplicaController (Client-seitig)

#### 🔧 Grundlegende API-Funktionen:

```lua
-- ReplicaController verfügbar machen
local ReplicaController = require(game.ReplicatedStorage.Modules.Libraries.ReplicaController)
```

#### **`ReplicaController.ReplicaOfClassCreated(className, listener)`**
- **🎯 Zweck**: Registriert einen Listener für eine bestimmte Replica-Klasse
- **📋 Parameter**: 
  - `className` (string): Name der Replica-Klasse (z.B. "PlayerData")
  - `listener` (function): Callback-Funktion, die aufgerufen wird
- **↩️ Rückgabe**: Connection-Objekt mit `Disconnect()` Methode
- **💡 Beispiel**:
```lua
local connection = ReplicaController.ReplicaOfClassCreated("PlayerData", function(replica)
    print("PlayerData Replica erhalten!")
    -- Hier kannst du mit der Replica arbeiten
end)
```

#### **`ReplicaController.GetReplicaById(id)`**
- **🎯 Zweck**: Holt eine Replica anhand ihrer ID
- **📋 Parameter**: `id` (number): Replica-ID
- **↩️ Rückgabe**: Replica-Objekt oder nil

#### **`ReplicaController.GetFirstReplicaOfClass(className)`**
- **🎯 Zweck**: Holt die erste Replica einer bestimmten Klasse
- **📋 Parameter**: `className` (string): Name der Klasse
- **↩️ Rückgabe**: Replica-Objekt oder nil
- **💡 Beispiel**:
```lua
local playerDataReplica = ReplicaController.GetFirstReplicaOfClass("PlayerData")
```

#### **`ReplicaController.HasClass(className)`**
- **🎯 Zweck**: Prüft, ob eine Replica-Klasse existiert
- **📋 Parameter**: `className` (string): Name der Klasse
- **↩️ Rückgabe**: boolean

#### **`ReplicaController.HasInitialData()`**
- **🎯 Zweck**: Prüft, ob die initialen Daten empfangen wurden
- **↩️ Rückgabe**: boolean

#### **`ReplicaController.RequestData()`**
- **🎯 Zweck**: Fordert Replica-Daten vom Server an
- **💡 Verwendung**: Wird automatisch aufgerufen, wenn PlayerData noch nicht existiert

### 2. 📦 Replica-Objekt (Client-seitig)

Jede Replica hat folgende Eigenschaften:
- `Data`: Die tatsächlichen Daten
- `Id`: Eindeutige ID der Replica
- `Class`: Klasse der Replica (z.B. "PlayerData")
- `Tags`: Zusätzliche Tags

#### **`Replica:ListenToChange(path, listener)`**
- **🎯 Zweck**: Hört auf Änderungen an einem bestimmten Pfad
- **📋 Parameter**:
  - `path` (string/table): Pfad zu den Daten (z.B. "Honey" oder {"Settings", "MuteMusic"})
  - `listener` (function): Callback mit (newValue, oldValue)
- **💡 Beispiel**:
```lua
replica:ListenToChange("Honey", function(newValue, oldValue)
    print("Honey geändert von", oldValue, "zu", newValue)
end)

replica:ListenToChange({"Settings", "MuteMusic"}, function(newValue, oldValue)
    print("MuteMusic Setting geändert zu:", newValue)
end)
```

#### **Replica:ListenToArrayInsert(path, listener)**
- **Zweck**: Hört auf neue Einträge in Arrays
- **Parameter**:
  - `path` (string/table): Pfad zum Array
  - `listener` (function): Callback mit (index, value)
- **Beispiel**:
```lua
replica:ListenToArrayInsert("Pets", function(index, petData)
    print("Neues Pet hinzugefügt:", petData)
end)
```

#### **Replica:ListenToNewKey(path, listener)**
- **Zweck**: Hört auf neue Keys in Objekten
- **Parameter**:
  - `path` (string/table): Pfad zum Objekt
  - `listener` (function): Callback mit (keyName, value)
- **Beispiel**:
```lua
replica:ListenToNewKey("SpawnUpgrades", function(keyName, value)
    print("Neuer Upgrade:", keyName, "=", value)
end)
```

## 💡 Praktische Beispiele

### 📊 PlayerData abrufen und überwachen:
```lua
local ReplicaController = require(game.ReplicatedStorage.Modules.Libraries.ReplicaController)

-- Warten auf PlayerData
ReplicaController.ReplicaOfClassCreated("PlayerData", function(replica)
    print("PlayerData geladen!")
    
    -- Aktuelle Daten abrufen
    local honey = replica.Data.Honey
    local pets = replica.Data.Pets
    
    -- Auf Änderungen hören
    replica:ListenToChange("Honey", function(newValue, oldValue)
        print("Honey Update:", oldValue, "->", newValue)
    end)
    
    replica:ListenToArrayInsert("Pets", function(index, petData)
        print("Neues Pet erhalten:", petData.PetName)
    end)
end)
```

### ⚙️ Settings überwachen:
```lua
ReplicaController.ReplicaOfClassCreated("PlayerData", function(replica)
    replica:ListenToChange({"Settings", "MuteMusic"}, function(newValue)
        if newValue then
            -- Musik stumm schalten
            game.SoundService.Music.Volume = 0
        else
            -- Musik einschalten
            game.SoundService.Music.Volume = 0.5
        end
    end)
end)
```

## ⚠️ Wichtige Hinweise

1. **🖥️ Client-seitig**: Du kannst nur auf Änderungen hören, nicht selbst Daten ändern
2. **🛤️ Pfade**: Verwende entweder String-Pfade ("Honey") oder Table-Pfade ({"Settings", "MuteMusic"})
3. **🔄 Synchronisation**: Das System wartet auf ClientReady, bevor Daten gesendet werden
4. **✅ ACK-System**: Client sendet Bestätigungen zurück an den Server
5. **🔧 ViRus Framework**: Das System ist in das ViRus Framework integriert

## 🐛 Debugging

Das System gibt Debug-Ausgaben aus:
- `[ReplicaController] 🔥 ReplicaData received` - Daten empfangen
- `[ReplicaController] PlayerData replica received!` - PlayerData spezifisch
- `[ReplicaController] Initial data marked as received!` - Initiale Daten bereit

## 🚨 Fehlerbehandlung

- Wenn `SetValue()` auf dem Client aufgerufen wird, gibt es eine Warnung aus
- Das System hat Fallback-Mechanismen für fehlende RemoteEvents
- Timeout-Schutz für ClientReady (15 Sekunden)

---

## 📚 Weitere Ressourcen

- [PlayerData System README](./PLAYERDATA_SYSTEM_README.md) - Server-seitige Datenverwaltung
- [System Documentation](./SYSTEM_DOCUMENTATION.md) - Gesamtsystem-Übersicht
