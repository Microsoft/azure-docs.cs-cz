---
title: Kontrolní seznam zabezpečení Azure databáze | Dokumentace Microsoftu
description: Tento článek obsahuje sadu kontrolní seznam pro zabezpečení služby Azure database.
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
ms.openlocfilehash: db976fb61c5659fbdcb7ba30aa4f619d52d441c4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252390"
---
# <a name="azure-database-security-checklist"></a>Kontrolní seznam zabezpečení Azure databáze

K vylepšení zabezpečení – Azure Database zahrnuje celou řadu integrovaných kontrolních mechanismů zabezpečení, které můžete použít k omezení a řízení přístupu.

Mezi ně patří:

-   Bránu firewall, která vám umožní vytvořit [pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) omezujících připojení podle IP adresy
-   Server brány firewall na úrovni přístupný z webu Azure portal
-   Pravidla brány firewall na úrovni databáze přístupné z aplikace SSMS
-   Zabezpečené připojení k databázi pomocí zabezpečeného připojovacích řetězců
-   Použití řízení přístupu
-   Šifrování dat
-   Auditování služby SQL Database
-   Detekce hrozeb SQL Database

## <a name="introduction"></a>Úvod
Cloud computing vyžaduje nové modely zabezpečení, které neznámého mnoha uživatelům aplikace, správci databází a programátory. V důsledku toho některé organizace mají váhají k implementaci cloudové infrastruktury pro správu dat z důvodu vnímaná bezpečnostní rizika. Ale velká část tento problém můžete zmírnit prostřednictvím lepší přehled o funkcích zabezpečení integrované do Microsoft Azure a Microsoft Azure SQL Database.

## <a name="checklist"></a>Kontrolní seznam
Doporučujeme, abyste si přečetli [osvědčené postupy zabezpečení Azure Database](https://docs.microsoft.com/azure/security/azure-database-security-best-practices) článku před revizí Tento kontrolní seznam. Budete moct na maximum Tento kontrolní seznam po pochopení osvědčených postupů. Potom můžete tento kontrolní seznam k Ujistěte se, že jsme vyřešili důležité problémy v zabezpečení služby Azure database.


|Kontrolní seznam kategorií| Popis|
| ------------ | -------- |
|**Ochrana dat**||
| <br> Šifrování během pohybu/přenosu| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), k šifrování dat, když se data přenášejí do sítí.</li><li>Databáze vyžaduje zabezpečené komunikace z klientů na základě [TDS (tabulkových dat Stream)](https://msdn.microsoft.com/library/dd357628.aspx) protokol přes protokol TLS (Transport Layer Security).</li></ul> |
|<br>Šifrování v klidovém stavu| <ul><li>[Transparentní šifrování dat](https://go.microsoft.com/fwlink/?LinkId=526242), pokud jsou neaktivní data fyzicky uložená v libovolné formě digitální.</li></ul>|
|**Řízení přístupu**||  
|<br> Přístup k databázi | <ul><li>[Ověřování](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) ověřování (ověřování Azure Active Directory) AD používá identity spravované v Azure Active Directory.</li><li>[Autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) uživatelům udělit nejmenší možná oprávnění.</li></ul> |
|<br>Přístup k aplikaci| <ul><li>[Úrovně zabezpečení řádků](https://msdn.microsoft.com/library/dn765131) (pomocí zásad zabezpečení, ve stejnou dobu omezení přístupu na úrovni řádků na základě identit, role nebo spuštění kontextu uživatele).</li><li>[Maskování dynamických dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (pomocí oprávnění a zásady, omezuje riziko ohrožení citlivých dat jejich maskováním pro neprivilegované uživatele)</li></ul>|
|**Proaktivní monitorování**||  
| <br>Detekce a sledování| <ul><li>[Auditování](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) sleduje události databáze a zapisuje je do protokolu auditu / log aktivity vašich [účtu služby Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).</li><li>Track – Azure Database stavu pomocí [protokolů aktivit Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Detekce hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze. </li></ul> |
|<br>Azure Security Center| <ul><li>[Data monitorování](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) použití služby Azure Security Center jako centralizované zabezpečení řešení pro monitorování pro SQL a dalším službám Azure.</li></ul>|       

## <a name="conclusion"></a>Závěr
Azure Database je robustní databázové platformě, s celou řadu funkcí zabezpečení, které splňují mnoho organizace i regulačních požadavků. Data můžete snadno chránit řízení fyzický přístup k vašim datům a použitím širokou škálu možností pro zabezpečení dat na soubory, sloupec – nebo na úrovni řádků pomocí transparentního šifrování dat, šifrování na úrovni buňky nebo zabezpečení na úrovní řádků. Vždy šifrovaný také umožní operace proti šifrovaná data, zjednodušuje proces aktualizace aplikace. Zase přístup k auditování protokoly aktivit SQL Database vám poskytne informace, které potřebujete, umožňuje vědět, jak a kdy se datům přistupuje.

## <a name="next-steps"></a>Další postup
Ochranu databáze před uživateli se zlými úmysly nebo neoprávněným přístupem můžete zlepšit v několika jednoduchých krocích. V tomto kurzu se naučíte:

- Nastavit [pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) pro váš server nebo databázi.
- Chraňte svoje data pomocí [šifrování](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Povolit [auditování služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

