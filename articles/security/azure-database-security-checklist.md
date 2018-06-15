---
title: Kontrolní seznam zabezpečení Azure databáze | Microsoft Docs
description: Tento článek obsahuje sadu kontrolní seznam pro zabezpečení databáze Azure.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 7712196caefeea8a62045d432d762fbd16f42011
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893962"
---
# <a name="azure-database-security-checklist"></a>Kontrolní seznam zabezpečení Azure databáze

K vylepšení zabezpečení, databáze Azure obsahuje několik předdefinovaných zabezpečení ovládacích prvků, které můžete použít k omezení a řízení přístupu.

Mezi ně patří:

-   Bránu firewall, která umožňuje vytvářet [pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) omezení připojení pomocí IP adresy
-   Server brány firewall na úrovni přístupný z portálu Azure
-   Pravidla brány firewall na úrovni databáze přístupné z aplikace SSMS
-   Zabezpečené připojení k databázi pomocí zabezpečené připojovací řetězce
-   Použijte správu přístupu
-   Šifrování dat
-   Auditování služby SQL Database
-   Detekce hrozeb databáze SQL

## <a name="introduction"></a>Úvod
Cloud computing vyžaduje nové vzorů zabezpečení, které jsou obeznámeni mnoho aplikace uživatelům, správce databáze a programátory v jazyce. V důsledku toho jsou některé organizace odhodlání k implementaci cloudové infrastruktury pro správu dat z důvodu dosahovaný bezpečnostní rizika. Velká část tuto situaci však můžete zmírnit prostřednictvím lepší pochopení funkce zabezpečení, které jsou integrovány do Microsoft Azure a Microsoft Azure SQL Database.

## <a name="checklist"></a>Kontrolní seznam
Doporučujeme, abyste si přečetli [osvědčené postupy zabezpečení databáze Azure](https://docs.microsoft.com/azure/security/azure-database-security-best-practices) článek před projdete tento kontrolní seznam. Bude moct využívat naplno Tento kontrolní seznam, Jakmile porozumíte osvědčené postupy. Pak můžete tento kontrolní seznam a ujistěte se, že jsme vyřešili důležité problémy v zabezpečení databáze Azure.


|Kontrolní seznam kategorie| Popis|
| ------------ | -------- |
|**Ochrana dat**||
| <br> Šifrování během pohybu nebo přenosu| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), pro šifrování dat, když se data přenášejí do sítí.</li><li>Databáze vyžaduje zabezpečené komunikace z klientů na základě [TDS (Tabular Data Stream)](https://msdn.microsoft.com/en-in/library/dd357628.aspx) protokol přes protokol TLS (Transport Layer Security).</li></ul> |
|<br>Šifrování v klidovém stavu| <ul><li>[Transparentní šifrování dat](http://go.microsoft.com/fwlink/?LinkId=526242), je-li neaktivní data fyzicky uložena v jakékoli digitální podobě.</li></ul>|
|**Řízení přístupu**||  
|<br> Přístup k databázi | <ul><li>[Ověřování](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) (Azure Active Directory Authentication) AD ověřování pomocí identity spravované službou Azure Active Directory.</li><li>[Autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) uživatelům uděluje nejnižší oprávnění potřebná.</li></ul> |
|<br>Přístup k aplikaci| <ul><li>[Řádek úroveň zabezpečení](https://msdn.microsoft.com/library/dn765131) (pomocí zásad zabezpečení, ve stejnou dobu omezení nízkoúrovňové přístupu na základě kontextu uživatele identit, role nebo spuštění).</li><li>[Dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (pomocí oprávnění & zásady, omezuje zranitelnost citlivá data pomocí maskování uživatelům bez oprávnění)</li></ul>|
|**Proaktivní monitorování**||  
| <br>Sledování & zjišťování| <ul><li>[Auditování](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) sleduje události databáze a zapisuje je do protokolu auditování / log aktivity vaší [účet úložiště Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).</li><li>Sledování databázi Azure stavu pomocí [protokoly aktivity monitorování Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Detekce hrozby](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) zjistila nezvyklé databázové aktivity, které indikují potenciální ohrožení databáze. </li></ul> |
|<br>Azure Security Center| <ul><li>[Data monitorování](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) použití služby Azure Security Center jako centralizované zabezpečení řešení monitorování pro SQL a jinými službami Azure.</li></ul>|       

## <a name="conclusion"></a>Závěr
Databáze Azure je platforma robustní databáze, s celou řadu funkcí zabezpečení, které splňují mnoho organizace i regulačních požadavků. Data můžete snadno chránit tak řízení fyzický přístup k datům a pomocí různých možností zabezpečení dat na soubor-, sloupec- nebo nízkoúrovňové transparentní šifrování dat, šifrování na úrovni buněk nebo zabezpečení na úrovni řádků. Vždy šifrovaný také umožní operace proti šifrovaná data, zjednodušuje proces aktualizace aplikace. Přístup k auditování protokoly aktivity databáze SQL se pak poskytuje informace, které potřebujete, abyste mohli vědět, jak a kdy je přístup k datům.

## <a name="next-steps"></a>Další postup
Ochranu databáze před uživateli se zlými úmysly nebo neoprávněným přístupem můžete zlepšit v několika jednoduchých krocích. V tomto kurzu se naučíte:

- Nastavit [pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) vašeho serveru nebo databáze.
- Ochrana dat s [šifrování](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Povolit [auditování databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

