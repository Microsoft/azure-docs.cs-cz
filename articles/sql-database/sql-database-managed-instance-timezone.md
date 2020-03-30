---
title: Časová pásma spravované instance
description: Informace o specifikách časového pásma spravované instance Azure SQL Database
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256091"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Časová pásma ve spravované instanci Azure SQL Database

Koordinovaný světový čas (UTC) je doporučené časové pásmo pro datovou vrstvu cloudových řešení. Azure SQL Database Managed Instance také nabízí výběr časových pásem, aby vyhovovaly potřebám existujících aplikací, které ukládají hodnoty data a času a funkce data a času volání s implicitním kontextem určitého časového pásma.

Funkce T-SQL jako [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) nebo KÓD CLR sledují časové pásmo nastavené na úrovni instance. Úlohy agenta serveru SQL Server také sledovat plány podle časového pásma instance.

  >[!NOTE]
  > Spravovaná instance je jedinou možností nasazení azure sql database, která podporuje nastavení časového pásma. Další možnosti nasazení vždy postupujte podle UTC.
Pokud potřebujete interpretovat informace o datu a čase v časovém pásmu mimo UTC, použijte [v časovém pásmu](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) v jednoasložkových databázích SQL.

## <a name="supported-time-zones"></a>Podporovaná časová pásma

Sada podporovaných časových pásem je zděděna z podkladového operačního systému spravované instance. Je pravidelně aktualizován, aby získal nové definice časových pásem a odrážel změny stávajících.

[Zásady změny letního času a časového pásma](https://aka.ms/time) zaručují historickou přesnost od roku 2010.

Seznam s názvy podporovaných časových pásem je vystaven prostřednictvím systémového zobrazení [sys.time_zone_info.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)

## <a name="set-a-time-zone"></a>Nastavení časového pásma

Časové pásmo spravované instance lze nastavit pouze během vytváření instance. Výchozí časové pásmo je UTC.

  >[!NOTE]
  > Časové pásmo existující spravované instance nelze změnit.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Nastavení časového pásma prostřednictvím portálu Azure

Když zadáte parametry pro novou instanci, vyberte časové pásmo ze seznamu podporovaných časových pásem.
  
![Nastavení časového pásma při vytváření instance](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Zadejte vlastnost timezoneId v [šabloně Správce prostředků,](https://aka.ms/sql-mi-create-arm-posh) chcete-li nastavit časové pásmo během vytváření instancí.

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

Seznam podporovaných hodnot pro vlastnost timezoneId je na konci tohoto článku.

Pokud není zadán, časové pásmo je nastaveno na Čas UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Kontrola časového pásma instance

Funkce [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) vrátí zobrazovaný název časového pásma instance.

## <a name="cross-feature-considerations"></a>Důležité informace o funkcích

### <a name="restore-and-import"></a>Obnovení a import

Záložní soubor můžete obnovit nebo importovat data do spravované instance z instance nebo ze serveru s jiným nastavením časového pásma. Disponujte s opatrností. Analyzujte chování aplikace a výsledky dotazů a sestav, stejně jako při přenosu dat mezi dvěma instancemi serveru SQL Server s různým nastavením časového pásma.

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Při provádění obnovení bodu v čase, čas obnovení je interpretován jako čas UTC. Tímto způsobem se vyhnete nejasnostem z důvodu letního času a jeho možných změn.

### <a name="auto-failover-groups"></a>Skupiny automatického převzetí služeb při selhání

Použití stejného časového pásma v primární a sekundární instanci ve skupině převzetí služeb při selhání není vynuceno, ale důrazně doporučujeme.

  >[!WARNING]
  > Důrazně doporučujeme použít stejné časové pásmo pro primární a sekundární instanci ve skupině převzetí služeb při selhání. Z důvodu některých případech vzácné použití zachování stejné časové pásmo v primární a sekundární instance není vynuceno. Je důležité si uvědomit, že v případě ručního nebo automatického převzetí služeb při selhání si sekundární instance zachová původní časové pásmo.

## <a name="limitations"></a>Omezení

- Časové pásmo existující spravované instance nelze změnit.
- Externí procesy spuštěné z úloh agenta serveru SQL Server nesledují časové pásmo instance.

## <a name="list-of-supported-time-zones"></a>Seznam podporovaných časových pásem

| **ID časového pásma** | **Zobrazovaný název časového pásma** |
| --- | --- |
| Dateline (běžný čas) | (UTC-12:00) Mezinárodní datum linka západ |
| UTC-11 | (UTC-11:00) Koordinovaný univerzální čas-11 |
| Aleutský standardní čas | (UTC-10:00) Aleutské ostrovy |
| Havajský standardní čas | (UTC-10:00) Havaj |
| Marquesas (běžný čas) | (UTC-09:30) Marquesas ostrovy |
| Aljašský standardní čas | (UTC-09:00) Aljaška |
| UTC-09 | (UTC-09:00) Koordinovaný univerzální čas-09 |
| Tichomoří (běžný čas) (Mexiko) | (UTC-08:00) Baja Kalifornie |
| UTC-08 | (UTC-08:00) Koordinovaný univerzální čas-08 |
| Tichomoří (běžný čas) | (UTC-08:00) Tichomoří (US & Kanada) |
| Americký horský standardní čas | (UTC-07:00) Arizona |
| Horský standardní čas (Mexiko) | (UTC-07:00) Čivava, La Paz, Mazatlan |
| Horský standardní čas | (UTC-07:00) Mountain Time (US & Kanada) |
| Střední Amerika (běžný čas) | (UTC-06:00) Střední Amerika |
| Centrální standardní čas | (UTC-06:00) Centrální čas (US & Kanada) |
| Velikonoční ostrov (běžný čas) | (UTC-06:00) Velikonoční ostrov |
| Centrální standardní čas (Mexiko) | (UTC-06:00) Guadalajara, Mexico City, Monterrey |
| Kanada centrální standardní čas | (UTC-06:00) Saskatchewan |
| SA Tichomoří (běžný čas) | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Východní standardní čas (Mexiko) | (UTC-05:00) Chetumal |
| Východní standardní čas | (UTC-05:00) Východní čas (USA & Kanada) |
| Haiti (běžný čas) | (UTC-05:00) Haiti |
| Kuba (běžný čas) | (UTC-05:00) Havana |
| Východní americký standardní čas | (UTC-05:00) Indiana (východ) |
| Turci a Caicos (běžný čas) | (UTC-05:00) Turci a Caicos |
| Paraguay (běžný čas) | (UTC-04:00) Asuncion |
| Atlantický standardní čas | (UTC-04:00) Atlantický čas (Kanada) |
| Venezuela (běžný čas) | (UTC-04:00) Caracas |
| Střední brazílie (běžný čas) | (UTC-04:00) Cuiaba |
| SA západní standardní čas | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Tichomoří SA (běžný čas) | (UTC-04:00) Santiago |
| Newfoundland (běžný čas) | (UTC-03:30) Newfoundland |
| Tocantins (běžný čas) | (UTC-03:00) Araguaina |
| E. Jižní Amerika (běžný čas) | (UTC-03:00) Brasilia |
| SA východní standardní čas | (UTC-03:00) Cayenne, Fortaleza |
| Argentina (běžný čas) | (UTC-03:00) Město Buenos Aires |
| Grónsko (běžný čas) | (UTC-03:00) Grónsko |
| Montevideo (běžný čas) | (UTC-03:00) Montevideo |
| Magallanes (běžný čas) | (UTC-03:00) Punta Arény |
| Saint Pierre (běžný čas) | (UTC-03:00) Svatý Pierre a Miquelon |
| Bahia (běžný čas) | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Koordinovaný univerzální čas-02 |
| Středoatlantický standardní čas | (UTC-02:00) Střední Atlantik - Starý |
| Azory (běžný čas) | (UTC-01:00) Azory |
| Kapverdy (běžný čas) | (UTC-01:00) Cabo Verde je. |
| UTC | Koordinovaný univerzální čas (UTC) |
| GMT (běžný čas) | (UTC+00:00) Dublin, Edinburgh, Lisabon, Londýn |
| Greenwichský standardní čas | (UTC+00:00) Monrovia, Reykjavík |
| W. Evropa (běžný čas) | (UTC+01:00) Amsterdam, Berlín, Bern, Řím, Stockholm, Vídeň |
| Střední Evropa (běžný čas) | (UTC+01:00) Bělehrad, Bratislava, Budapešť, Lublaň, Praha |
| Romance (běžný čas) | (UTC+01:00) Brusel, Kodaň, Madrid, Paříž |
| Maroko (běžný čas) | (UTC+01:00) Casablanca |
| Sao Tome (běžný čas) | (UTC+01:00) Sao Tome |
| Středoevropský standardní čas | (UTC+01:00) Sarajevo, Skopje, Varšava, Záhřeb |
| W. Střední Afrika (běžný čas) | (UTC+01:00) Západní střední Afrika |
| Jordánsko (běžný čas) | (UTC+02:00) Amman |
| STANDARDNÍ ČAS GTB | (UTC+02:00) Atény, Bukurešť |
| Střední východ (běžný čas) | (UTC+02:00) Bejrút |
| Egypt (běžný čas) | (UTC+02:00) Káhira |
| E. Evropa (běžný čas) | (UTC+02:00) Kišiněv |
| Sýrie (běžný čas) | (UTC+02:00) Damašku |
| Standardní čas západního břehu Jordánu | (UTC+02:00) Gaza, Hebron |
| Jihoafrická republika (běžný čas) | (UTC+02:00) Harare, Pretoria |
| FLE (běžný čas) | (UTC+02:00) Helsinky, Kyjev, Riga, Sofie, Tallinn, Vilnius |
| Izrael (běžný čas) | (UTC+02:00) Jeruzalém |
| Kaliningradský standardní čas | (UTC+02:00) Kaliningrad |
| Súdán (běžný čas) | (UTC+02:00) Chartúm |
| Libye (běžný čas) | (UTC+02:00) Tripoli |
| Namibie (běžný čas) | (UTC+02:00) Windhoek |
| Arabský standardní čas | (UTC+03:00) Bagdádu |
| Turecko (běžný čas) | (UTC+03:00) Istanbul |
| Arabský standardní čas | (UTC+03:00) Kuvajt, Rijád |
| Bělorusko (běžný čas) | (UTC+03:00) Minsk |
| Ruský standardní čas | (UTC+03:00) Moskva, Petrohrad |
| E. Afrika (běžný čas) | (UTC+03:00) Nairobi |
| Írán (běžný čas) | (UTC+03:30) Teheránu |
| Arabský standardní čas | (UTC+04:00) Abú Zabí, Maskat |
| Astrachaň (běžný čas) | (UTC+04:00) Astrachaň, Uljanovsk |
| Ázerbájdžán (běžný čas) | (UTC+04:00) Baku |
| Časové pásmo Rusko 3 | (UTC+04:00) Iževsk, Samara |
| Mauricius (běžný čas) | (UTC+04:00) Přístav Louis |
| Saratov (běžný čas) | (UTC+04:00) Saratov |
| Gruzínský standardní čas | (UTC+04:00) Tbilisi |
| Volgograd (běžný čas) | (UTC+04:00) Volgograd |
| Kavkaz (běžný čas) | (UTC+04:00) Jerevan |
| Afghánistán (běžný čas) | (UTC+04:30) Kábulu |
| Západní Asie (běžný čas) | (UTC+05:00) Ašchabad, Taškent |
| Jekatěrinburg (běžný čas) | (UTC+05:00) Ekaterinburg |
| Pákistán (běžný čas) | (UTC+05:00) Islámábád, Karáčí |
| Indie (běžný čas) | (UTC+05:30) Chennai, Kalkata, Bombaj, Nové Dillí |
| Srí Lanka (běžný čas) | (UTC+05:30) Šrí Jayawardenepura |
| Nepál (běžný čas) | (UTC+05:45) Kathmandu |
| Střední Asie (běžný čas) | (UTC+06:00) Astana |
| Bangladéš (běžný čas) | (UTC+06:00) Dháka |
| Omsk (běžný čas) | (UTC+06:00) Omsk |
| Myanmar (běžný čas) | (UTC+06:30) Rangún (Rangún) |
| SE Asie (běžný čas) | (UTC+07:00) Bangkok, Hanoj, Jakarta |
| Altai (běžný čas) | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Mongolsko (běžný čas) | (UTC+07:00) Hovd (Hovd) |
| Severní Asie (běžný čas) | (UTC+07:00) Krasnoyarsk |
| N. Střední Asie (běžný čas) | (UTC+07:00) Novosibirsk |
| Tomsk (běžný čas) | (UTC+07:00) Tomsk |
| Čína (běžný čas) | (UTC+08:00) Peking, Čchung-čching, Hongkong, Urumqi |
| Severovýchodní severní Asie (běžný čas) | (UTC+08:00) Irkutsk |
| Singapur (běžný čas) | (UTC+08:00) Kuala Lumpur, Singapur |
| W. Austrálie (běžný čas) | (UTC+08:00) Perth |
| Tchaj-pej (běžný čas) | (UTC+08:00) Taipei |
| Ulaanbaatar (běžný čas) | (UTC+08:00) Ulánbátar |
| Aus Central W. (běžný čas) | (UTC+08:45) Eucla (Eucla) |
| Transbajkal (běžný čas) | (UTC+09:00) Chita |
| Tokio (běžný čas) | (UTC+09:00) Osaka, Sapporo, Tokio |
| Severní Korea (běžný čas) | (UTC+09:00) Pchjongjangu |
| Korea (běžný čas) | (UTC+09:00) Soul |
| Jakutsk (běžný čas) | (UTC+09:00) Yakutsk |
| Cen. Austrálie (běžný čas) | (UTC+09:30) Adelaide |
| Centrální standardní čas AUS | (UTC+09:30) Darwin |
| E. Austrálie (běžný čas) | (UTC+10:00) Brisbane |
| Východní standardní čas AUS | (UTC+10:00) Canberra, Melbourne, Sydney |
| Západní Tichomoří (běžný čas) | (UTC+10:00) Guam, Port Moresby |
| Tasmánie (běžný čas) | (UTC+10:00) Hobart |
| Vladivostok (běžný čas) | (UTC+10:00) Vladivostok |
| Lord Howe (běžný čas) | (UTC+10:30) Ostrov Lorda Howea |
| Bougainville (běžný čas) | (UTC+11:00) Ostrov Bougainville |
| Rusko časové pásmo 10 | (UTC+11:00) Chokurdakh (Chokurdakh) |
| Magadan (běžný čas) | (UTC+11:00) Magadan |
| Norfolk (běžný čas) | (UTC+11:00) Norfolkský ostrov |
| Sachalin (běžný čas) | (UTC+11:00) Sachalin |
| Střední Tichomoří (běžný čas) | (UTC+11:00) Šalamoun Is., Nová Kaledonie |
| Rusko časové pásmo 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamčatský |
| Nový Zéland (běžný čas) | (UTC+12:00) Auckland(Wellington) – ovesné |
| UTC+12 | (UTC+12:00) Koordinovaný světový čas+12 |
| Fidži (běžný čas) | (UTC+12:00) Fidži |
| Kamčatka (běžný čas) | (UTC+12:00) Petropavlovsk-Kamčatský - Starý |
| Chathamské ostrovy (běžný čas) | (UTC+12:45) Chathamské ostrovy |
| UTC+13 | (UTC+13:00) Koordinovaný světový čas+13 |
| Tonga (běžný čas) | (UTC+13:00) Nuku'alofa |
| Samoa (běžný čas) | (UTC+13:00) Samoa |
| Line Islands (běžný čas) | (UTC+14:00) Ostrov Kiritimati |

## <a name="see-also"></a>Viz také 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [ČASOVÉ PÁSMO (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
