---
title: Kontrolní seznam zabezpečení Azure Database | Microsoft Docs
description: Pomocí kontrolního seznamu zabezpečení Azure Database se ujistěte, že řešíte důležité problémy se zabezpečením cloud computingu.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 80455b442bbfb9c8a7d40799b2ddd5fc25460578
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595580"
---
# <a name="azure-database-security-checklist"></a>Kontrolní seznam zabezpečení Azure Database

Pro lepší zabezpečení zahrnuje Azure Database řadu integrovaných ovládacích prvků zabezpečení, které můžete použít k omezení a řízení přístupu.

Tady jsou některé z nich:

-    Bránu firewall, která umožňuje vytvářet [pravidla brány firewall](../../azure-sql/database/firewall-configure.md) s omezením připojení podle IP adresy,
-    Brána firewall na úrovni serveru dostupná z Azure Portal
-    Pravidla brány firewall na úrovni databáze přístupná z SSMS
-    Zabezpečené připojení k databázi pomocí připojovacích řetězců zabezpečení
-    Použití správy přístupu
-    Šifrování dat
-    Auditování služby SQL Database
-    SQL Database detekce hrozeb

## <a name="introduction"></a>Úvod
Cloud computing vyžaduje nové paradigma zabezpečení, které neznají mnoho uživatelů aplikací, správců databází a programátoři. V důsledku toho se některé organizace váhají k implementaci cloudové infrastruktury pro správu dat kvůli zjištěným bezpečnostním rizikům. Mnohé z těchto záležitostí je ale možné vyřešit lepším porozuměním funkcím zabezpečení integrovaným do Microsoft Azure a Microsoft Azure SQL Database.

## <a name="checklist"></a>Kontrolní seznam
Doporučujeme, abyste si před kontrolou tohoto kontrolního seznamu přečetli článek věnované [osvědčeným postupům zabezpečení Azure Database](../../azure-sql/database/security-best-practice.md)  . Až porozumíte osvědčeným postupům, budete moct získat co nejvíc tohoto kontrolního seznamu. Tento kontrolní seznam pak můžete použít k tomu, abyste se ujistili, že byly vyřešeny důležité problémy v zabezpečení databáze Azure.


|Kategorie kontrolního seznamu| Description|
| ------------ | -------- |
|**Ochrana dat**||
| <br> Šifrování při pohybu nebo přenosu| <ul><li>[Transport Layer Security](/windows-server/security/tls/transport-layer-security-protocol)pro šifrování dat při přesunu dat do sítí.</li><li>Databáze vyžaduje zabezpečenou komunikaci od klientů založenou na protokolu [TDS (Tabular data Stream)](/openspecs/windows_protocols/ms-tds/893fcc7e-8a39-4b3c-815a-773b7b982c50) přes protokol TLS (Transport Layer Security).</li></ul> |
|<br>Šifrování neaktivních uložených dat| <ul><li>[Transparentní šifrování dat](../../azure-sql/database/transparent-data-encryption-tde-overview.md), když se neaktivní data ukládají fyzicky do libovolné digitální formy.</li></ul>|
|**Řízení přístupu**||  
|<br> Přístup k databázi | <ul><li>[Ověřování (Azure Active Directory](../../azure-sql/database/logins-create-manage.md) ověřování) ověřování AD používá identity spravované pomocí Azure Active Directory.</li><li>[Autorizace](../../azure-sql/database/logins-create-manage.md) uděluje uživatelům nejnižší potřebná oprávnění.</li></ul> |
|<br>Přístup k aplikaci| <ul><li>[Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) (pomocí zásad zabezpečení zároveň omezuje přístup na úrovni řádků na základě identity, role nebo kontextu spuštění uživatele).</li><li>[Dynamické maskování dat](../../azure-sql/database/dynamic-data-masking-overview.md) (pomocí zásad & oprávnění, omezování ohrožení citlivých dat jejich maskování pro uživatele bez oprávnění)</li></ul>|
|**Proaktivní monitorování**||  
| <br>Sledování & zjišťování| <ul><li>[Auditování](../../azure-sql/database/auditing-overview.md) sleduje události databáze a zapisuje je do protokolu auditu nebo protokolu aktivit v [účtu Azure Storage](../../storage/common/storage-account-create.md).</li><li>Sledujte službu Azure Database Health pomocí [Azure monitor protokolů aktivit](../../azure-monitor/essentials/platform-logs-overview.md).</li><li>[Detekce hrozeb](../../azure-sql/database/threat-detection-configure.md) detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze. </li></ul> |
|<br>Azure Security Center| <ul><li>[Monitorování dat](../../security-center/security-center-remediate-recommendations.md) Jako centralizované řešení pro monitorování zabezpečení pro SQL a další služby Azure použijte Azure Security Center.</li></ul>|        

## <a name="conclusion"></a>Závěr
Azure Database je robustní databázová platforma s plnou škálou funkcí zabezpečení, které splňují mnoho požadavků organizace a dodržování předpisů. Data můžete snadno chránit pomocí řízení fyzického přístupu k datům a pomocí různých možností zabezpečení dat na úrovni souboru, sloupce nebo řádku pomocí transparentní šifrování dat, Cell-Level šifrování nebo Row-Level zabezpečení. Always Encrypted také umožňuje operace proti šifrovaným datům, což zjednodušuje proces aktualizací aplikace. Přístup k protokolům auditování SQL Database aktivity navíc poskytuje informace, které potřebujete, což vám umožní zjistit, jak a kdy se k datům přistupuje.

## <a name="next-steps"></a>Další kroky
Ochranu databáze před uživateli se zlými úmysly nebo neoprávněným přístupem můžete zlepšit v několika jednoduchých krocích. V tomto kurzu se naučíte:

- Nastavte [pravidla brány firewall](../../azure-sql/database/firewall-configure.md) pro váš server a databázi.
- Zabezpečte svá data pomocí [šifrování](/sql/relational-databases/security/encryption/sql-server-encryption).
- Povolte [auditování SQL Database](../../azure-sql/database/auditing-overview.md).