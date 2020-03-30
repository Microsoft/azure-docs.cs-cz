---
title: Kontrolní seznam zabezpečení databáze Azure| Dokumenty společnosti Microsoft
description: Tento článek obsahuje sadu kontrolního seznamu pro zabezpečení databáze Azure.
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
ms.openlocfilehash: d9283a36d5f7ccb82b2cc211485487d5a3dcce7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79201021"
---
# <a name="azure-database-security-checklist"></a>Kontrolní seznam zabezpečení databáze Azure

Azure Database obsahuje řadu integrovaných ovládacích prvků zabezpečení, které můžete použít k omezení a řízení přístupu.

Mezi ně patří:

-    Brána firewall, která umožňuje vytvářet [pravidla brány firewall](../../sql-database/sql-database-firewall-configure.md) omezující připojení podle adresy IP,
-    Brána firewall na úrovni serveru přístupná z portálu Azure
-    Pravidla brány firewall na úrovni databáze přístupná ze služby SSMS
-    Zabezpečené připojení k databázi pomocí zabezpečených připojovacích řetězců
-    Použití správy přístupu
-    Šifrování dat
-    Auditování služby SQL Database
-    Detekce hrozeb databáze SQL

## <a name="introduction"></a>Úvod
Cloud computing vyžaduje nová bezpečnostní paradigmata, která jsou pro mnoho uživatelů aplikací, správců databází a programátorů neznámá. V důsledku toho některé organizace váhají s implementací cloudové infrastruktury pro správu dat kvůli vnímaným bezpečnostním rizikům. Velkou část této obavy však lze zmírnit lepšípochopení funkcí zabezpečení integrovaných do Microsoft Azure a Microsoft Azure SQL Database.

## <a name="checklist"></a>Kontrolní seznam
Doporučujeme, abyste si před kontrolou tohoto kontrolního seznamu přečetli článek Doporučené [postupy zabezpečení databáze Azure.](database-best-practices.md) Budete mít možnost získat co nejvíce z tohoto kontrolního seznamu poté, co porozumíte osvědčeným postupům. Tento kontrolní seznam pak můžete použít k ujistěte se, že jste vyřešili důležité problémy v zabezpečení databáze Azure.


|Kategorie kontrolního seznamu| Popis|
| ------------ | -------- |
|**Ochrana dat**||
| <br> Šifrování v pohybu/tranzitu| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), pro šifrování dat při přesouvání dat do sítí.</li><li>Databáze vyžaduje zabezpečenou komunikaci od klientů na základě protokolu [TDS (Tabulkový datový proud)](https://msdn.microsoft.com/library/dd357628.aspx) přes TLS (Transport Layer Security).</li></ul> |
|<br>Šifrování v klidovém stavu| <ul><li>[Transparentní šifrování dat](https://go.microsoft.com/fwlink/?LinkId=526242), pokud jsou neaktivní data fyzicky uložena v jakékoli digitální podobě.</li></ul>|
|**Řízení přístupu**||  
|<br> Přístup k databázi | <ul><li>[Ověřování](../../sql-database/sql-database-manage-logins.md) (Ověřování Azure Active Directory) Ověřování služby AD používá identity spravované službou Azure Active Directory.</li><li>[Autorizace](../../sql-database/sql-database-manage-logins.md) uděluje uživatelům nejméně potřebná oprávnění.</li></ul> |
|<br>Přístup k aplikacím| <ul><li>[Zabezpečení na úrovni řádku](https://msdn.microsoft.com/library/dn765131) (pomocí zásad zabezpečení současně omezuje přístup na úrovni řádku na základě identity uživatele, role nebo kontextu spuštění).</li><li>[Dynamické maskování dat](../../sql-database/sql-database-dynamic-data-masking-get-started.md) (pomocí zásad & oprávnění omezuje vystavení citlivých dat maskováním neprivilegovaným uživatelům)</li></ul>|
|**Proaktivní monitorování**||  
| <br>Sledování & detekce| <ul><li>[Auditování](../../sql-database/sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditování nebo protokolu aktivit ve vašem [účtu Úložiště Azure](../../storage/common/storage-create-storage-account.md).</li><li>Sledujte stav databáze Azure pomocí [protokolů aktivit azure monitoru](../../azure-monitor/platform/platform-logs-overview.md).</li><li>[Detekce hrozeb](../../sql-database/sql-database-threat-detection.md) detekuje neobvyklé databázové aktivity označující potenciální ohrožení zabezpečení databáze. </li></ul> |
|<br>Azure Security Center| <ul><li>[Monitorování dat](../../security-center/security-center-enable-auditing-on-sql-databases.md) Azure Security Center můžete použít jako centralizované řešení monitorování zabezpečení pro SQL a další služby Azure.</li></ul>|        

## <a name="conclusion"></a>Závěr
Azure Database je robustní databázová platforma s celou řadou funkcí zabezpečení, které splňují mnoho požadavků na dodržování předpisů v organizaci a předpisech. Data můžete snadno chránit řízením fyzického přístupu k datům a použitím různých možností zabezpečení dat na úrovni souboru, sloupce nebo řádku pomocí transparentního šifrování dat, šifrování na úrovni buněk nebo zabezpečení na úrovni řádků. Vždy šifrované také umožňuje operace proti šifrovaným datům, což zjednodušuje proces aktualizací aplikací. Přístup k protokolům auditování aktivity databáze SQL vám zase poskytuje informace, které potřebujete, což vám umožní vědět, jak a kdy jsou data přístupná.

## <a name="next-steps"></a>Další kroky
Ochranu databáze před uživateli se zlými úmysly nebo neoprávněným přístupem můžete zlepšit v několika jednoduchých krocích. V tomto kurzu se naučíte:

- Nastavte [pravidla brány firewall](../../sql-database/sql-database-firewall-configure.md) pro server a databázi.
- Chraňte svá data [šifrováním](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Povolte [auditování databáze SQL](../../sql-database/sql-database-auditing.md).

