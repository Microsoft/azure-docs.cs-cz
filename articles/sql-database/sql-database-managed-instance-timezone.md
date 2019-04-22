---
title: Azure SQL Database Managed Instance časové pásmo | Microsoft Docs"
description: Další informace o časovém pásmu specifika Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/10/2019
ms.openlocfilehash: 23314e97051da95ab164baeab6e9d089f486351a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489686"
---
# <a name="time-zone-in-azure-sql-database-managed-instance-preview"></a>Časové pásmo ve spravované instanci Azure SQL Database (Preview)

Při použití koordinovaný univerzální čas (UTC) je doporučený postup pro datovou vrstvu Cloudová řešení, Azure SQL Database Managed Instance nabízí široký výběr časové pásmo pro potřeby existující aplikace, které ukládají hodnoty datum a čas a datum hovoru a časové funkce s implicitní kontextu konkrétní časové pásmo.

Funkce jazyka T-SQL, jako jsou [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) nebo kódu CLR sledovat časovému pásmu nastavenému na instanci úrovni. Úlohy agenta SQL serveru také podle plánu podle časového pásma instance.

  >[!NOTE]
  > Managed Instance je možnost pouze nasazení služby Azure SQL Database, která podporuje nastavení časového pásma. Další možnosti nasazení vždy postupujte podle standardu UTC.
Použití [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) v jedné a ve fondu databází SQL potřebujete interpretovat informace o datu a času v časovém pásmu než UTC.

## <a name="supported-time-zones"></a>Podporované časových pásem

Sadu podporovaných časových pásem je zděděno od základního operačního systému spravovanou instanci a je pravidelně aktualizovali k získání nových definic časové pásmo a odráží změny do již existující.

Seznam s názvy podporované časových pásem je k dispozici prostřednictvím [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) systémové zobrazení.

## <a name="setting-time-zone"></a>Nastavení časového pásma

Časové pásmo pro spravovanou instanci je možné nastavit při vytváření instance pouze. Výchozí časové pásmo je koordinovaný univerzální čas (UTC).

  >[!NOTE]
  > Časové pásmo existující spravovanou instanci nejde změnit.

### <a name="setting-the-time-zone-through-azure-portal"></a>Nastavení časového pásma prostřednictvím webu Azure portal

Při zadávání parametrů pro nové instance, vyberte ze seznamu podporovaných časových pásem časové pásmo:
  
![Nastavení časového pásma během vytvoření instance](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Zadejte vlastnost ID časové zóny v vaše [šablony Resource Manageru](https://aka.ms/sql-mi-create-arm-posh) během vytvoření instance nastavit časové pásmo.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Seznam podporovaných hodnot pro vlastnost ID časové zóny najdete na konci tohoto článku.

Pokud není zadán, nastaví se časové pásmo na čas UTC.

## <a name="checking-the-time-zone-of-instance"></a>Kontrola časové pásmo instance

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) funkce vrátí zobrazovaný název časového pásma instance.

## <a name="cross-feature-considerations"></a>Důležité informace o různé – funkce

### <a name="restore-and-import"></a>Obnovení a import

Dokáže obnovit záložní soubor nebo importovat data do spravované instance z instance nebo serveru s nastavením jiného časového pásma. Nicméně Ujistěte se, že s rozmyslem a k analýze chování aplikace a výsledky dotazů a sestav, stejně jako při přenosu dat mezi dvěma instancemi SQL serveru s nastavením jiného časového pásma.

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Když provádíte obnovení k určitému bodu v čase, čas k obnovení je interpretován jako čas UTC, aby se zabránilo veškerou nejednoznačnost kvůli letního času a jeho potenciálních změn.

### <a name="auto-failover-groups"></a>Skupiny automatického převzetí služeb při selhání

Pomocí stejného časového pásma mezi primární a sekundární instance v převzetí služeb při selhání skupiny se nevynucuje, ale důrazně se doporučuje.
  >[!IMPORTANT]
  > I když existují platné scénáře s jiném časovém pásmu na geograficky sekundární instance použít pro škálování čtení pouze, mějte prosím na paměti, že v případě ručního nebo automatického převzetí služeb při selhání na sekundární instanci jej zachovat jeho původní časové pásmo.

## <a name="limitations"></a>Omezení

- Časové pásmo existující spravovanou instanci nejde změnit.
- Externí proces spuštěn z úlohy agenta SQL nedodržují časové pásmo instance.
- Spravovaná Instance je nativní [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance) rutinu Powershellu není čas podpory předávání zóna parametr ještě. Použití Powershellu obálky s [šablony Resource Manageru](https://aka.ms/sql-mi-create-arm-posh) místo.
- Rozhraní příkazového řádku [az sql mi vytvořit](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-create) zatím nepodporuje parametr časového pásma.

## <a name="list-of-supported-time-zones"></a>Seznam podporovaných časových pásem

| **ID časového pásma** | **Zobrazovaný název časového pásma** |
| --- | --- |
| Datová hranice (běžný čas) | (UTC-12:00) Západní mezinárodní časová osa |
| UTC-11 | (UTC-11:00) Koordinovaný univerzální čas-11 |
| Aleutské ostrovy (běžný čas) | (UTC-10:00) Aleutské ostrovy |
| Havajské ostrovy (běžný čas) | (UTC-10:00) Havajské ostrovy |
| Markézy (běžný čas) | (UTC-09:30) Souostroví Markézy |
| Aljaška (běžný čas) | (UTC-09:00) Aljaška |
| UTC-09 | (UTC-09:00) Koordinovaný univerzální čas (UTC)-09 |
| Tichomoří – Mexiko (běžný čas) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Koordinovaný univerzální čas (UTC)-08 |
| Tichomoří (běžný čas) | (UTC-08:00) Tichomoří (USA a Kanada) |
| Horská oblast USA (běžný čas) | (UTC-07:00) Arizona |
| Horská oblast – Mexiko (běžný čas) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Horská oblast (běžný čas) | (UTC-07:00) Horská oblast USA a Kanady |
| Střední Amerika (běžný čas) | (UTC-06:00) Střední Amerika |
| Střední oblast (běžný čas) | (UTC-06:00) Střed USA a Kanady |
| Velikonoční ostrov (běžný čas) | (UTC-06:00) Velikonoční ostrov |
| Střední oblast – Mexiko (běžný čas) | (UTC-06:00) Guadalajara, Mexico City, Monterrey |
| Střední Kanada (běžný čas) | (UTC-06:00) Saskatchewan |
| Tichomoří J. Amerika (běžný čas) | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Východní oblast – Mexiko (běžný čas) | (UTC-05:00) Chetumal |
| Východní oblast (běžný čas) | (UTC-05:00) Východ USA a Kanady |
| Haitský běžný čas | (UTC-05:00) Haiti |
| Kuba (běžný čas) | (UTC-05:00) Havana |
| Východ USA (běžný čas) | (UTC-05:00) Indiana (východ) |
| Turks a Caicos (běžný čas) | (UTC-05:00) Turks a Caicos |
| Paraguay (běžný čas) | (UTC-04:00) Asunción |
| Atlantik (běžný čas) | (UTC-04:00) Atlantik (Kanada) |
| Venezuela (běžný čas) | (UTC-04:00) Caracas |
| Střední Brazílie (běžný čas) | (UTC-04:00) Cuiaba |
| Západní J. Amerika (běžný čas) | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Tichomoří Jižní Am. (běžný čas) | (UTC-04:00) Santiago |
| Newfoundland (běžný čas) | (UTC-03:30) Newfoundland |
| Tocantins (běžný čas) | (UTC-03:00) Araguaína |
| E. Jižní Amerika (běžný čas) | (UTC-03:00) Brasília |
| Východní J. Amerika (běžný čas) | (UTC-03:00) Cayenne, Fortaleza |
| Argentina (běžný čas) | (UTC-03:00) Buenos Aires |
| Grónsko (běžný čas) | (UTC-03:00) Grónsko |
| Montevideo (běžný čas) | (UTC-03:00) Montevideo |
| Magallanský standardní čas | (UTC-03:00) Punta Arenas |
| Saint Pierre (běžný čas) | (UTC-03:00) Saint Pierre a Miquelon |
| Bahia (běžný čas) | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Koordinovaný univerzální čas (UTC)-02 |
| Střední Atlantik (běžný čas) | (UTC-02:00) Střední Atlantik – starý čas |
| Azory (běžný čas) | (UTC-01:00) Azory |
| Cabo Verde (běžný čas) | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Koordinovaný univerzální čas |
| Střední čas (běžný čas) | (UTC+00:00) Dublin, Edinburgh, Lisabon, Londýn |
| Střední (běžný čas) | (UTC+00:00) Monrovia, Reykjavík |
| W. Evropa (běžný čas) | (UTC+01:00) Amsterdam, Berlín, Bern, Řím, Stockholm, Vídeň |
| Střední Evropa (běžný čas) | (UTC+01:00) Bělehrad, Bratislava, Budapešť, Lublaň, Praha |
| Románské země (běžný čas) | (UTC+01:00) Brusel, Kodaň, Madrid, Paříž |
| Maroko (běžný čas) | (UTC+01:00) Casablanca |
| Svatý Tomáš, standardní čas | (UTC+01:00) Svatý Tomáš |
| Střední Evropa (běžný čas) | (UTC+01:00) Sarajevo, Skopje, Varšava, Záhřeb |
| W. Střední Afriky (běžný čas) | (UTC+01:00) Západ střední Afriky |
| Jordánsko (běžný čas) | (UTC+02:00) Ammán |
| Balkán (běžný čas) | (UTC+02:00) Atény, Bukurešť |
| Střední východ (běžný čas) | (UTC+02:00) Bejrút |
| Egypt (běžný čas) | (UTC+02:00) Káhira |
| E. Evropa (běžný čas) | (UTC+02:00) Kišiněv |
| Sýrie (běžný čas) | (UTC+02:00) Damašek |
| Západní břeh (běžný čas) | (UTC+02:00) Gaza, Hebron |
| Jihoafrická republika (běžný čas) | (UTC+02:00) Harare, Pretoria |
| Finsko (běžný čas) | (UTC+02:00) Helsinky, Kyjev, Riga, Sofie, Tallinn, Vilnius |
| Izrael (běžný čas) | (UTC+02:00) Jeruzalém |
| Kaliningrad (běžný čas) | (UTC+02:00) Kaliningrad |
| Súdánský standardní čas | (UTC+02:00) Chartúm |
| Libye (běžný čas) | (UTC+02:00) Tripolis |
| Namibie (běžný čas) | (UTC+02:00) Windhoek |
| Arabský poloostrov (běžný čas) | (UTC+03:00) Bagdád |
| Turecko (běžný čas) | (UTC+03:00) Istanbul |
| Oblast Arábie (běžný čas) | (UTC+03:00) Kuvajt, Rijád |
| Bělorusko (běžný čas) | (UTC+03:00) Minsk |
| Rusko (běžný čas) | (UTC+03:00) Moskva, St. Petersburg |
| E. Afrika (běžný čas) | (UTC+03:00) Nairobi |
| Irán (běžný čas) | (UTC+03:30) Teherán |
| Arábie (běžný čas) | (UTC+04:00) Abú Dhabi, Muskat |
| Astrachaňský běžný čas | (UTC+04:00) Astrachaň, Uljanovsk |
| Ázerbájdžán (běžný čas) | (UTC+04:00) Baku |
| Rusko časové pásmo 3 | (UTC+04:00) Iževsk, Samara |
| Běžný čas Mauritius | (UTC+04:00) Port Louis |
| Saratovský standardní čas | (UTC+04:00) Saratov |
| Gruzie (běžný čas) | (UTC+04:00) Tbilisi |
| Volgogradský standardní čas | (UTC+04:00) Volgograd |
| Kavkaz (běžný čas) | (UTC+04:00) Jerevan |
| Afghánistán (běžný čas) | (UTC+04:30) Kábul |
| Západní Asie (běžný čas) | (UTC+05:00) Ašchabád, Taškent |
| Jekatěrinburg (běžný čas) | (UTC+05:00) Jekatěrinburg |
| Pákistán (běžný čas) | (UTC+05:00) Islámábád, Karáčí |
| Indie (běžný čas) | (UTC+05:30) Čennaj, Kalkata, Bombaj, Nové Dillí |
| Srí Lanka (běžný čas) | (UTC+05:30) Šrí Džajavardanapura |
| Nepál (běžný čas) | (UTC+05:45) Káthmándú |
| Střední Asie (běžný čas) | (UTC+06:00) Astana |
| Bangladéš (běžný čas) | (UTC+06:00) Dháka |
| Omsk (běžný čas) | (UTC+06:00) Omsk |
| Myanmar (běžný čas) | (UTC+06:30) Yangon (Rangún) |
| Jihovýchodní Asie (běžný čas) | (UTC+07:00) Bangkok, Hanoj, Jakarta |
| Altajský běžný čas | (UTC+07:00) Barnaul, Gorno-Altajsk |
| W. Mongolsko (běžný čas) | (UTC+07:00) Chovd |
| Severní Asie (běžný čas) | (UTC+07:00) Krasnojarsk |
| N. Střední Asie (běžný čas) | (UTC+07:00) Novosibirsk |
| Tomská oblast (běžný čas) | (UTC+07:00) Tomsk |
| Čína (běžný čas) | (UTC+08:00) Peking, Čchung-čching, Hongkong, Urumči |
| Východ severní Asie (běžný čas) | (UTC+08:00) Irkutsk |
| Singapore (běžný čas) | (UTC+08:00) Kuala Lumpur, Singapur |
| W. Austrálie (běžný čas) | (UTC+08:00) Perth |
| Taipei (běžný čas) | (UTC+08:00) Tchaj-pej |
| Ulánbátar (běžný čas) | (UTC+08:00) Ulánbátar |
| Středozápad Austr. (běžný čas) | (UTC+08:45) Eucla |
| Zabajkalský běžný čas | (UTC+09:00) Čita |
| Tokio (běžný čas) | (UTC+09:00) Ósaka, Sapporo, Tokio |
| Severní Korea (běžný čas) | (UTC+09:00) Pchjongjang |
| Jižní Korea (běžný čas) | (UTC+09:00) Soul |
| Jakutsk (běžný čas) | (UTC+09:00) Jakutsk |
| Cen. Austrálie (běžný čas) | (UTC+09:30) Adelaide |
| Střední Austrálie (běžný čas) | (UTC+09:30) Darwin |
| E. Austrálie (běžný čas) | (UTC+10:00) Brisbane |
| Východní Austrálie (běžný čas) | (UTC+10:00) Canberra, Melbourne, Sydney |
| Západní Tichomoří (běžný čas) | (UTC+10:00) Guam, Port Moresby |
| Tasmánie (běžný čas) | (UTC+10:00) Hobart |
| Vladivostok Standard Time | (UTC+10:00) Vladivostok |
| Lord Howe (běžný čas) | (UTC+10:30) Ostrov Lorda Howa |
| Bougainville (běžný čas) | (UTC+11:00) Bougainville |
| Rusko časové pásmo 10 | (UTC+11:00) Čokurdach |
| Magadan (běžný čas) | (UTC+11:00) Magadan |
| Norfolk (běžný čas) | (UTC+11:00) Norfolk |
| Sachalin (běžný čas) | (UTC+11:00) Sachalin |
| Střední Tichomoří (běžný čas) | (UTC+11:00) Šalamounovy ostrovy, Nová Kaledonie |
| Rusko časové pásmo 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamčatskij |
| Nový Zéland (běžný čas) | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Koordinovaný univerzální čas+12 |
| Fidži (běžný čas) | (UTC+12:00) Fidži |
| Kamčatka (běžný čas) | (UTC+12:00) Petropavlovsk-Kamčatskij – starý čas |
| Chathamovy ostrovy (běžný čas) | (UTC+12:45) Chathamské ostrovy |
| UTC+13 | (UTC+13:00) Koordinovaný univerzální čas (UTC) + 13 |
| Tonga (běžný čas) | (UTC+13:00) Nuku'alofa |
| Samoa (běžný čas) | (UTC+13:00) Samoa |
| Ostrovy Line (běžný čas) | (UTC+14:00) Ostrov Kiritimati |

## <a name="see-also"></a>Další informace najdete v tématech

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [NA časové pásmo (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)