---
title: Ochrana služby Azure SQL a dat ve službě Azure Security Center | Dokumentace Microsoftu
description: Tato dokument řeší doporučení ve službě Azure Security Center, které vám pomohou chránit data a služby Azure SQL a zůstaňte souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: terrylan
ms.openlocfilehash: 177deb779ca3e3e9575a41ab9a37bb51d5e79df8
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008075"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Ochrana služby Azure SQL a dat ve službě Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol.  Doporučení platí pro typy prostředků Azure: virtuální počítače (VM), sítě, SQL a datům a aplikacím.

Tento článek se zabývá doporučení, která se vztahují na služby Azure SQL a data. System center doporučení týkající se povolení auditování pro servery Azure SQL Server a databáze, povolení šifrování databází SQL a povolení šifrování účtu úložiště Azure.  V následující tabulce použijte jako odkaz vám pomůžou pochopit dostupné doporučení služby a dat SQL a každý z nich, co dělá když je použijete.
### <a name="monitor-data-security"></a>Monitorování zabezpečení dat

Po kliknutí na **Zabezpečení dat** v části **Prevence** se otevře okno **Datové prostředky** s doporučeními pro SQL a službu Storage. Také obsahuje [doporučení](security-center-sql-service-recommendations.md) pro obecný stav databáze. Další informace o šifrování úložiště najdete v tématu [Povolení šifrování účtu úložiště Azure v Azure Security Center](security-center-enable-encryption-for-storage-account.md).

![Datové prostředky](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

V části **Doporučení SQL** můžete kliknout na jakékoli doporučení a získáte další podrobnosti o další akci pro řešení problému. Následující příklad ukazuje rozbalení doporučení **Auditování databáze a detekce hrozeb u databází SQL**.

![Podrobnosti o doporučení SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Okno **Povolit auditování a detekci hrozeb u databází SQL** obsahuje následující informace:

* Seznam databází SQL
* Server, na kterém jsou umístěné
* Informace o tom, zda bylo toto nastavení zděděno ze serveru nebo zda je v této databázi jedinečné
* Aktuální stav
* Závažnosti problému

Když kliknete na databázi, abyste vyřešili toto doporučení, otevře se okno **Auditování a detekce hrozeb**, jak je znázorněno na následující obrazovce.

![Auditování a detekce hrozeb](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Pokud chcete povolit auditování, vyberte **Zapnuto** pod možností **Auditování**.

## <a name="data-and-storage-recommendations"></a>Doporučení pro data a úložiště

|Typ prostředku|Bezpečnostní skóre|Doporučení|Popis|
|----|----|----|----|
|Účet úložiště|20|Vyžádání bezpečného přenosu do účtu úložiště|Zabezpečený přenos je možnost, která vynutí účtu úložiště tak, aby přijímal požadavky jenom ze zabezpečeného připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a služby a chrání přenášená data ze sítě vrstvy útoky man-in-the-middle, odposlouchávání a napadení relace.|
|Redis|20|Povolit pouze zabezpečená připojení k mezipaměti Redis|Povolte pouze připojení přes protokol SSL pro Redis Cache. Použití zabezpečeného připojení zajišťuje ověřování mezi serverem a služby a chrání přenášená data ze sítě vrstvy útoky man-in-the-middle, odposlouchávání a napadení relace.|
|SQL|15|Povolit transparentní šifrování dat v databázích SQL|Povolte transparentní šifrování dat, aby se dala chránit neaktivní uložená data a splnit požadavky na dodržování předpisů.|
|SQL|15|Povolení auditování pro servery SQL|Povolte auditování pro servery Azure SQL. (Pouze služby azure SQL. Nezahrnuje běžící na virtuálních počítačích SQL.)|
|SQL|15|Povolení auditování pro databáze SQL|Povolte auditování pro databáze Azure SQL. (Pouze služby azure SQL. Nezahrnuje běžící na virtuálních počítačích SQL.)|
|Data lake analytics|15|Povolit šifrování v klidovém stavu z Data Lake Analytics|Povolte transparentní šifrování dat k zabezpečení dat v klidovém stavu v vaše Data Lake Analytics. Šifrování v klidovém stavu je transparentní, což znamená, že Data Lake Analytics automaticky šifruje data před uložením a dešifruje před načtením. Neexistují žádné změny v v aplikací a služby, které pracují s Data Lake Analytics z důvodu šifrování. Šifrování v klidovém stavu minimalizuje se riziko úniku dat z fyzických odcizení a také pomáhá plnit požadavky na dodržování legislativních předpisů.|
|Data lake store|15|Povolit šifrování v klidovém stavu pro Data Lake Store|Povolte transparentní šifrování dat k zabezpečení dat v klidovém stavu v vaše Data Lake Store. Šifrování v klidovém stavu je transparentní, což znamená, že se Data Lake Store automaticky šifruje data před uložením a dešifruje před načtením. Nemusíte nic měnit aplikace a služby, které pracují s Data Lake Store tak, aby vyhovovaly šifrování. Šifrování v klidovém stavu minimalizuje se riziko úniku dat z fyzických odcizení a také pomáhá plnit požadavky na dodržování legislativních předpisů.|
|Účet úložiště|15|Povolit šifrování pro účet úložiště Azure|Povolení šifrování služby Azure Storage pro neaktivní uložená data. Šifrování služby Storage (SSE) funguje tak, že šifrování dat, když se zapisují do úložiště Azure a dešifruje před načítání. SSE je momentálně dostupná jenom pro službu Azure Blob service a lze použít pro objekty BLOB bloku, objekty BLOB stránky a doplňovací objekty BLOB.|
|Data lake analytics|5|Povolit diagnostické protokoly v Data Lake Analytics|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Data lake store|5|Povolení protokolování diagnostiky v Azure Data Lake Store|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|SQL|30|Náprava ohrožení zabezpečení na vašich databází SQL|Posouzení ohrožení zabezpečení SQL vyhledá vaší databáze na ohrožení zabezpečení a zpřístupňuje všechny odchylky od osvědčených postupů, jako jsou chybné konfigurace, nadměrná oprávnění a nechráněné citlivá data. Řešení zjištěná ohrožení zabezpečení může výrazně zlepšit vaše zásadní roli zabezpečení databáze.|
|SQL|20|Zřízení správce Azure AD pro SQL server|Zřízení správce Azure AD pro SQL server povolit ověřování Azure AD. Ověřování Azure AD umožňuje správu oprávnění zjednodušené a centralizované identity management uživatelů databáze a dalším službám společnosti Microsoft.|
|Účet úložiště|15|Zakázat síť neomezený přístup k účtu úložiště|Audit neomezený přístup k síti v nastavení brány firewall na účtu úložiště. Místo toho nakonfigurujte pravidla sítě, abyste účtu úložiště můžou přistupovat jenom aplikace z povolených sítí. Povolit připojení z konkrétní Internetu nebo místní klienty, můžete udělit přístup k provoz z konkrétní služby Azure virtual networks a rozsahy adres veřejné internetové IP.|
|Účet úložiště|1||Migrace účtů úložiště do nových prostředků v AzureRM|Použití nové verze 2 Azure Resource Manageru, pro vaše účty úložiště kvůli vylepšení zabezpečení: silnější access control (RBAC), lepší auditování, nasazení založené na Resource Manageru a zásad správného řízení, přístup ke spravovaným identitám přístup k trezoru klíčů pro tajné kódy, ověřování Azure AD na základě a podpora značek a skupiny prostředků pro snadnější správu zabezpečení.|



## <a name="see-also"></a>Další informace najdete v tématech
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Ochrana virtuálních počítačů pomocí Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrana aplikací pomocí Azure Security Center](security-center-application-recommendations.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
