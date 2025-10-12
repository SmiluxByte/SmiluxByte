# 📊 PlayerData System - API Dokumentation

## 🔍 Übersicht
Das PlayerData-System verwaltet alle Spielerdaten und synchronisiert sie über das ReplicationController-System. Es basiert auf ProfileService für Datenspeicherung und ReplicaService für Replikation.

## 📁 Wichtige Datei
- **Server**: `ServerStorage/Modules/PlayerData`

## 🚀 Grundlegende Verwendung

```lua
local dataHandler = require(game.ServerStorage.Modules.PlayerData)

-- System initialisieren
dataHandler:Init()
```

## ⚙️ Hauptfunktionen

### 1. 📥 Daten abrufen

#### **`dataHandler:Get(player, key)`**
- **🎯 Zweck**: Holt einen einzelnen Wert aus den Spielerdaten
- **📋 Parameter**: 
  - `player` (Player): Der Spieler
  - `key` (string): Der Schlüssel (z.B. "Honey", "Clicks")
- **↩️ Rückgabe**: Der Wert oder nil
- **💡 Beispiel**:
```lua
local honey = dataHandler:Get(player, "Honey")
local clicks = dataHandler:Get(player, "Clicks")
local settings = dataHandler:Get(player, "Settings")
```

#### **`dataHandler:Get2(player)`**
- **🎯 Zweck**: Holt alle Spielerdaten
- **📋 Parameter**: `player` (Player): Der Spieler
- **↩️ Rückgabe**: Komplettes Daten-Table
- **💡 Beispiel**:
```lua
local allData = dataHandler:Get2(player)
print("Spieler hat", allData.Honey, "Honey")
```

#### **`dataHandler:GetProfile(player)`**
- **🎯 Zweck**: Holt das komplette Profile-Objekt (für erweiterte Operationen)
- **📋 Parameter**: `player` (Player): Der Spieler
- **↩️ Rückgabe**: Profile-Objekt mit `Profile` und `Replica`
- **💡 Beispiel**:
```lua
local profile = dataHandler:GetProfile(player)
local replica = profile.Replica
local profileData = profile.Profile.Data
```

### 2. 📝 Daten setzen

#### **`dataHandler:Set(player, key, value)`**
- **🎯 Zweck**: Setzt einen Wert in den Spielerdaten
- **📋 Parameter**:
  - `player` (Player): Der Spieler
  - `key` (string): Der Schlüssel
  - `value` (any): Der neue Wert
- **💡 Beispiel**:
```lua
dataHandler:Set(player, "Honey", 1000)
dataHandler:Set(player, "Clicks", 500)
dataHandler:Set(player, "MasteryLevel", 5)
```

#### **`dataHandler:Update(player, key, callback)`**
- **🎯 Zweck**: Aktualisiert einen Wert basierend auf dem alten Wert
- **📋 Parameter**:
  - `player` (Player): Der Spieler
  - `key` (string): Der Schlüssel
  - `callback` (function): Funktion die (oldValue) -> newValue macht
- **💡 Beispiel**:
```lua
-- Honey um 100 erhöhen
dataHandler:Update(player, "Honey", function(oldHoney)
    return oldHoney + 100
end)

-- Clicks verdoppeln
dataHandler:Update(player, "Clicks", function(oldClicks)
    return oldClicks * 2
end)
```

#### **`dataHandler:TempUpdate(player, key, callback)`**
- **🎯 Zweck**: Aktualisiert temporäre Leaderboard-Daten
- **📋 Parameter**:
  - `player` (Player): Der Spieler
  - `key` (string): Der Schlüssel
  - `callback` (function): Callback-Funktion
- **💡 Beispiel**:
```lua
dataHandler:TempUpdate(player, "TempClicks", function(oldValue)
    return oldValue + 1
end)
```

### 3. 🐝 Pet-System

#### **`dataHandler:AddPet(player, petTable, finalIndex)`**
- **🎯 Zweck**: Fügt ein neues Pet hinzu
- **📋 Parameter**:
  - `player` (Player): Der Spieler
  - `petTable` (table): Pet-Daten
  - `finalIndex` (string, optional): Spezifischer Index
- **↩️ Rückgabe**: Pet-Index
- **💡 Beispiel**:
```lua
local petData = {
    PetId = "Bee_001",
    PetName = "Cool Bee",
    PetType = "Bee",
    PetExp = 0,
    IsLocked = false
}
local petIndex = dataHandler:AddPet(player, petData)
```

#### **dataHandler:RemovePet(player, petIndex)**
- **Zweck**: Entfernt ein Pet
- **Parameter**:
  - `player` (Player): Der Spieler
  - `petIndex` (string): Pet-Index
- **Rückgabe**: boolean (true wenn Pet existierte)
- **Beispiel**:
```lua
local wasRemoved = dataHandler:RemovePet(player, "pet_123")
```

#### **dataHandler:RenamePet(player, petIndex, newName)**
- **Zweck**: Benennt ein Pet um
- **Parameter**:
  - `player` (Player): Der Spieler
  - `petIndex` (string): Pet-Index
  - `newName` (string): Neuer Name (bereits gefiltert)
- **Beispiel**:
```lua
dataHandler:RenamePet(player, "pet_123", "Super Bee")
```

#### **dataHandler:LockPet(player, petIndex, isLocked)**
- **Zweck**: Sperrt/entsperrt ein Pet
- **Parameter**:
  - `player` (Player): Der Spieler
  - `petIndex` (string): Pet-Index
  - `isLocked` (boolean): Lock-Status
- **Beispiel**:
```lua
dataHandler:LockPet(player, "pet_123", true) -- Pet sperren
```

#### **dataHandler:AddSerial(player, petIndex, serial)**
- **Zweck**: Fügt eine Seriennummer zu einem Pet hinzu
- **Parameter**:
  - `player` (Player): Der Spieler
  - `petIndex` (string): Pet-Index
  - `serial` (number): Seriennummer
- **Beispiel**:
```lua
dataHandler:AddSerial(player, "pet_123", 12345)
```

### 4. Upgrade-System

#### **dataHandler:GiveUpgrade(player, upgradeTable, upgradeName)**
- **Zweck**: Gibt ein Upgrade
- **Parameter**:
  - `player` (Player): Der Spieler
  - `upgradeTable` (string): Upgrade-Tabelle (z.B. "SpawnUpgrades")
  - `upgradeName` (string): Upgrade-Name
- **Beispiel**:
```lua
dataHandler:GiveUpgrade(player, "SpawnUpgrades", "ClickMulti")
dataHandler:GiveUpgrade(player, "SpawnUpgrades", "HoneyMulti")
```

### 5. Settings-System

#### **dataHandler:ChangeSetting(player, settingName, value)**
- **Zweck**: Ändert eine Einstellung
- **Parameter**:
  - `player` (Player): Der Spieler
  - `settingName` (string): Setting-Name
  - `value` (boolean): Neuer Wert
- **Beispiel**:
```lua
dataHandler:ChangeSetting(player, "MuteMusic", true)
dataHandler:ChangeSetting(player, "HidePopups", false)
```

### 6. Pass-System

#### **dataHandler:GivePass(player, passName)**
- **Zweck**: Gibt einen Pass
- **Parameter**:
  - `player` (Player): Der Spieler
  - `passName` (string): Pass-Name
- **Beispiel**:
```lua
dataHandler:GivePass(player, "VIP")
dataHandler:GivePass(player, "x2XP")
```

#### **dataHandler:RemoveTradingPass(player, pass)**
- **Zweck**: Entfernt einen Trading-Pass
- **Parameter**:
  - `player` (Player): Der Spieler
  - `pass` (string): Pass-Name
- **Rückgabe**: boolean (true wenn entfernt)
- **Beispiel**:
```lua
local removed = dataHandler:RemoveTradingPass(player, "VIP")
```

### 7. Quest-System

#### **dataHandler:CollectQuest(player, timeFrame, index)**
- **Zweck**: Markiert einen Quest als gesammelt
- **Parameter**:
  - `player` (Player): Der Spieler
  - `timeFrame` (string): "Daily" oder "Weekly"
  - `index` (number): Quest-Index
- **Beispiel**:
```lua
dataHandler:CollectQuest(player, "Daily", 1)
```

### 8. 🎒 Items-System

#### **`dataHandler:Get(player, "Items")`**
- **🎯 Zweck**: Holt alle Items des Spielers
- **📋 Parameter**: `player` (Player): Der Spieler
- **↩️ Rückgabe**: Table mit allen Items
- **💡 Beispiel**:
```lua
local items = dataHandler:Get(player, "Items")
print("Spieler hat", #items, "Items")
```

#### **Items hinzufügen (über Replica)**
- **Zweck**: Fügt ein neues Item hinzu
- **Parameter**:
  - `player` (Player): Der Spieler
  - `itemData` (table): Item-Daten
- **Beispiel**:
```lua
local profile = dataHandler:GetProfile(player)
local itemData = {
    ItemId = "Potion_001",
    ItemName = "Health Potion",
    ItemType = "Potion",
    Quantity = 1,
    Rarity = "Common"
}
profile.Replica:ArrayInsert({"Items"}, itemData)
```

#### **Items entfernen (über Replica)**
- **Zweck**: Entfernt ein Item aus dem Inventar
- **Parameter**:
  - `player` (Player): Der Spieler
  - `itemIndex` (number): Index des Items im Array
- **Beispiel**:
```lua
local profile = dataHandler:GetProfile(player)
local items = dataHandler:Get(player, "Items")
for i, item in ipairs(items) do
    if item.ItemId == "Potion_001" then
        profile.Replica:ArrayRemove({"Items"}, i)
        break
    end
end
```

#### **Item-Quantity ändern**
- **Zweck**: Ändert die Anzahl eines Items
- **Parameter**:
  - `player` (Player): Der Spieler
  - `itemIndex` (number): Index des Items
  - `newQuantity` (number): Neue Anzahl
- **Beispiel**:
```lua
local profile = dataHandler:GetProfile(player)
profile.Replica:SetValue({"Items", 1, "Quantity"}, 5)
```

#### **Items durchsuchen**
- **Zweck**: Findet Items nach bestimmten Kriterien
- **Parameter**:
  - `player` (Player): Der Spieler
  - `searchFunction` (function): Suchfunktion
- **Beispiel**:
```lua
local items = dataHandler:Get(player, "Items")
local potions = {}
for i, item in ipairs(items) do
    if item.ItemType == "Potion" then
        table.insert(potions, {index = i, item = item})
    end
end
```

### 9. Farm System

#### **dataHandler:GetFarmData(player)**
- **Zweck**: Holt Farm-Daten
- **Parameter**: `player` (Player): Der Spieler
- **Rückgabe**: Farm-Daten-Table
- **Beispiel**:
```lua
local farmData = dataHandler:GetFarmData(player)
print("Farmer Level:", farmData.FarmerLevel)
```

#### **dataHandler:UpgradeFarmer(player)**
- **Zweck**: Upgraded den Farmer (max Level 5)
- **Parameter**: `player` (Player): Der Spieler
- **Rückgabe**: boolean (true wenn erfolgreich)
- **Beispiel**:
```lua
local upgraded = dataHandler:UpgradeFarmer(player)
```

#### **dataHandler:UpgradeField(player, fieldType)**
- **Zweck**: Upgraded ein Feld
- **Parameter**:
  - `player` (Player): Der Spieler
  - `fieldType` (string): "Strawberry", "Apple", oder "Orange"
- **Rückgabe**: boolean (true wenn erfolgreich)
- **Beispiel**:
```lua
local upgraded = dataHandler:UpgradeField(player, "Strawberry")
```

### 10. Secret Chest System

#### **dataHandler:CollectSecretChest(player, chestName)**
- **Zweck**: Markiert eine Secret Chest als gesammelt
- **Parameter**:
  - `player` (Player): Der Spieler
  - `chestName` (string): Chest-Name
- **Beispiel**:
```lua
dataHandler:CollectSecretChest(player, "SecretChest_1")
```

#### **dataHandler:IsSecretChestCollected(player, chestName)**
- **Zweck**: Prüft, ob eine Secret Chest gesammelt wurde
- **Parameter**:
  - `player` (Player): Der Spieler
  - `chestName` (string): Chest-Name
- **Rückgabe**: boolean
- **Beispiel**:
```lua
local collected = dataHandler:IsSecretChestCollected(player, "SecretChest_1")
```

## 🎒 Items-System - Detaillierte Anleitung

### 📋 Items-Datenstruktur
Items werden als Array in `playerData.Items` gespeichert. Jedes Item hat folgende Struktur:
```lua
{
    ItemId = "Potion_001",        -- Eindeutige Item-ID
    ItemName = "Health Potion",   -- Anzeigename
    ItemType = "Potion",          -- Item-Kategorie
    Quantity = 1,                 -- Anzahl
    Rarity = "Common",            -- Seltenheit
    -- Weitere Eigenschaften je nach Item-Typ
}
```

### ⚙️ Häufige Item-Operationen

#### **➕ Item hinzufügen (mit Duplikat-Prüfung)**
```lua
local function addItem(player, itemData)
    local profile = dataHandler:GetProfile(player)
    local items = dataHandler:Get(player, "Items")
    
    -- Prüfen ob Item bereits existiert
    for i, existingItem in ipairs(items) do
        if existingItem.ItemId == itemData.ItemId then
            -- Quantity erhöhen
            profile.Replica:SetValue({"Items", i, "Quantity"}, existingItem.Quantity + itemData.Quantity)
            return true
        end
    end
    
    -- Neues Item hinzufügen
    profile.Replica:ArrayInsert({"Items"}, itemData)
    return true
end
```

#### **➖ Item entfernen (mit Quantity-Prüfung)**
```lua
local function removeItem(player, itemId, quantity)
    local profile = dataHandler:GetProfile(player)
    local items = dataHandler:Get(player, "Items")
    
    for i, item in ipairs(items) do
        if item.ItemId == itemId then
            if item.Quantity > quantity then
                -- Quantity reduzieren
                profile.Replica:SetValue({"Items", i, "Quantity"}, item.Quantity - quantity)
            else
                -- Item komplett entfernen
                profile.Replica:ArrayRemove({"Items"}, i)
            end
            return true
        end
    end
    return false
end
```

#### **🔍 Item-Quantity prüfen**
```lua
local function getItemQuantity(player, itemId)
    local items = dataHandler:Get(player, "Items")
    
    for _, item in ipairs(items) do
        if item.ItemId == itemId then
            return item.Quantity
        end
    end
    return 0
end
```

#### **🏷️ Items nach Typ filtern**
```lua
local function getItemsByType(player, itemType)
    local items = dataHandler:Get(player, "Items")
    local filteredItems = {}
    
    for i, item in ipairs(items) do
        if item.ItemType == itemType then
            table.insert(filteredItems, {index = i, item = item})
        end
    end
    
    return filteredItems
end
```

### 🎯 Items für Quest-System verwenden
```lua
-- Quest: "Sammle 5 Health Potions"
local function checkPotionQuest(player)
    local potionQuantity = getItemQuantity(player, "Potion_001")
    return potionQuantity >= 5
end

-- Quest: "Verwende 3 Mana Potions"
local function useManaPotion(player)
    if removeItem(player, "Mana_001", 1) then
        -- Mana wiederherstellen
        dataHandler:Update(player, "Mana", function(oldMana)
            return math.min(oldMana + 50, 100) -- Max 100 Mana
        end)
        return true
    end
    return false
end
```

## 📋 Datenstruktur (dataTemplate)

Das System verwaltet folgende Hauptkategorien:

### 💰 Währungen
- `Clicks`, `Honey`, `TimeCorns`, `Tickets`, `Stars`, `Rockets`
- `TotalClicks`, `TotalHoney`, `TotalTimeCorns`, etc.

### 🐝 Pets
- `Pets`: Table mit allen Pets
- `EquippedPets`: Array mit ausgerüsteten Pet-Indizes
- `Index`: Array mit allen Pet-IDs
- `TotalIndexed`: Anzahl indexierter Pets

### ⬆️ Upgrades
- `SpawnUpgrades`: Table mit Spawn-Upgrades
- `Mastery`: Table mit Mastery-Levels
- `PrestigeLevel`: Prestige-Level

### ⚙️ Settings
- `Settings`: Table mit allen Einstellungen
- `TutorialDone`: Tutorial-Status
- `HasCompletedTutorial`: Tutorial-Abschluss

### 🎒 Items
- `Items`: Array mit allen Items des Spielers
- Jedes Item hat: `ItemId`, `ItemName`, `ItemType`, `Quantity`, `Rarity`

### 🌾 Farm System
- `FarmData`: Table mit Farm-Daten
- `FarmerLevel`: Farmer-Level (0-5)
- `FieldUpgrades`: Feld-Upgrades
- `FieldStates`: Feld-Status

## ⚠️ Wichtige Hinweise

1. **🔄 Automatische Replikation**: Alle Änderungen werden automatisch an den Client gesendet
2. **📊 Leaderstats**: Bestimmte Werte werden automatisch in Leaderstats aktualisiert
3. **💾 ProfileService**: Daten werden automatisch gespeichert
4. **🔒 Thread-Safe**: Alle Operationen sind thread-safe
5. **✅ Validation**: Das System validiert Daten vor dem Speichern

## 🚨 Fehlerbehandlung

- `assert(Profiles[player], "Profile does not exist")` - Prüft ob Profile existiert
- Alle Funktionen geben `nil` oder `false` zurück bei Fehlern
- Warnungen werden ausgegeben bei ungültigen Operationen

## 🐛 Debugging

Das System gibt Debug-Ausgaben aus:
- `📊 PlayerData replica created for player: [Name]`
- `[PlayerDataInit] [Name] [✓ @ X.XXXs] PlayerData init complete`

---

## 📚 Weitere Ressourcen

- [ReplicationController README](./REPLICATION_CONTROLLER_README.md) - Client-seitige Replikation
- [System Documentation](./SYSTEM_DOCUMENTATION.md) - Gesamtsystem-Übersicht
