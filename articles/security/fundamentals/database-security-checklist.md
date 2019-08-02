---
title: Kontrolní seznam zabezpečení Azure Database | Microsoft Docs
description: Tento článek poskytuje sadu kontrolních seznamů pro zabezpečení Azure Database.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 424453e70e5b62e408f408cd5ae8169cddb14dd7
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68615665"
---
# <a name="azure-database-security-checklist"></a>Kontrolní seznam zabezpečení Azure Database

Pro lepší zabezpečení zahrnuje Azure Database řadu integrovaných ovládacích prvků zabezpečení, které můžete použít k omezení a řízení přístupu.

Mezi ně patří:

-   Bránu firewall, která umožňuje vytvářet [pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) s omezením připojení podle IP adresy,
-   Brána firewall na úrovni serveru dostupná z Azure Portal
-   Pravidla brány firewall na úrovni databáze přístupná z SSMS
-   Zabezpečené připojení k databázi pomocí připojovacích řetězců zabezpečení
-   Použití správy přístupu
-   Šifrování dat
-   Auditování služby SQL Database
-   Detekce hrozeb SQL Database

## <a name="introduction"></a>Úvod
Cloud computing vyžaduje nové paradigma zabezpečení, které neznají mnoho uživatelů aplikací, správců databází a programátoři. V důsledku toho se některé organizace váhají k implementaci cloudové infrastruktury pro správu dat kvůli zjištěným bezpečnostním rizikům. Mnohé z těchto záležitostí je ale možné vyřešit lepším porozuměním funkcím zabezpečení integrovaným do Microsoft Azure a Microsoft Azure SQL Database.

## <a name="checklist"></a>Kontrolní seznam
Doporučujeme, abyste si před kontrolou tohoto kontrolního seznamu přečetli článek věnované [osvědčeným postupům zabezpečení Azure Database](https://docs.microsoft.com/azure/security/azure-database-security-best-practices) . Až porozumíte osvědčeným postupům, budete moct získat co nejvíc tohoto kontrolního seznamu. Tento kontrolní seznam pak můžete použít k tomu, abyste se ujistili, že byly vyřešeny důležité problémy v zabezpečení databáze Azure.


|Kategorie kontrolního seznamu| Popis|
| ------------ | -------- |
|**Ochrana dat**||
| <br> Šifrování při pohybu nebo přenosu| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol)pro šifrování dat při přesunu dat do sítí.</li><li>Databáze vyžaduje zabezpečenou komunikaci od klientů založenou na protokolu [TDS (Tabular data Stream)](https://msdn.microsoft.com/library/dd357628.aspx) přes protokol TLS (Transport Layer Security).</li></ul> |
|<br>Šifrování v klidovém stavu| <ul><li>[Transparentní šifrování dat](https://go.microsoft.com/fwlink/?LinkId=526242), když se neaktivní data ukládají fyzicky do libovolné digitální formy.</li></ul>|
|**Řízení přístupu**||  
|<br> Přístup k databázi | <ul><li>[Ověřování](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) (Azure Active Directory ověřování) Ověřování AD používá identity spravované pomocí Azure Active Directory.</li><li>[Autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) uděluje uživatelům nejnižší potřebná oprávnění.</li></ul> |
|<br>Přístup k aplikaci| <ul><li>[Zabezpečení na úrovni řádků](https://msdn.microsoft.com/library/dn765131) (Pomocí zásad zabezpečení, ve stejnou dobu omezení přístupu na úrovni řádků na základě identity, role nebo kontextu spuštění uživatele).</li><li>[Dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (Použití zásad & oprávnění, omezování rizika citlivých dat jejich maskování pro uživatele bez oprávnění)</li></ul>|
|**Proaktivní monitorování**||  
| <br>Sledování & zjišťování| <ul><li>[Auditování](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) sleduje události databáze a zapisuje je do protokolu auditu nebo protokolu aktivit v [účtu Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).</li><li>Sledujte službu Azure Database Health pomocí [Azure monitor protokolů aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Detekce hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze. </li></ul> |
|<br>Azure Security Center| <ul><li>[Monitorování dat](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) Jako centralizované řešení pro monitorování zabezpečení pro SQL a další služby Azure použijte Azure Security Center.</li></ul>|       

## <a name="conclusion"></a>Závěr
Azure Database je robustní databázová platforma s plnou škálou funkcí zabezpečení, které splňují mnoho požadavků organizace a dodržování předpisů. Data můžete snadno chránit pomocí řízení fyzického přístupu k datům a pomocí různých možností zabezpečení dat na úrovni souboru, sloupce nebo řádku pomocí transparentní šifrování dat, šifrování na úrovni buňky nebo zabezpečení na úrovni řádků. Always Encrypted také umožňuje operace proti šifrovaným datům, což zjednodušuje proces aktualizací aplikace. Přístup k protokolům auditování SQL Database aktivity navíc poskytuje informace, které potřebujete, což vám umožní zjistit, jak a kdy se k datům přistupuje.

## <a name="next-steps"></a>Další postup
Ochranu databáze před uživateli se zlými úmysly nebo neoprávněným přístupem můžete zlepšit v několika jednoduchých krocích. V tomto kurzu se naučíte:

- Nastavte [pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) pro váš server a databázi.
- Zabezpečte svá data pomocí [šifrování](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Povolte [auditování SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

