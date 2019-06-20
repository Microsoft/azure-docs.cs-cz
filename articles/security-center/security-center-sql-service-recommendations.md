---
title: Ochrana dat a úložiště služby Azure ve službě Azure Security Center | Dokumentace Microsoftu
description: Tato dokument řeší doporučení ve službě Azure Security Center, které vám pomohou chránit data a služby Azure SQL a zůstaňte souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: monhaber
ms.openlocfilehash: 2ac0e4ebaafb8b0c9c79e885cecbefc5a65c1823
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275336"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Ochrana dat a úložiště služby Azure ve službě Azure Security Center
V tomto tématu se dozvíte, jak zobrazit a implementace doporučení zabezpečení pro data a prostředky úložiště. Azure Security Center najít tato doporučení při analýze stavu zabezpečení vašich prostředků Azure.

## <a name="view-your-data-security-information"></a>Zobrazit informace o zabezpečení dat

1. V **kontrolu zabezpečení prostředků** klikněte na tlačítko **Data a prostředky úložiště**.

   ![Datové prostředky a prostředky úložiště](./media/security-center-monitoring/click-data.png)

    **Zabezpečení dat** otevře se stránka s doporučeními pro datové prostředky.

     ![Datové prostředky](./media/security-center-monitoring/sql-overview.png)

Na této stránce můžete:

* Klikněte na tlačítko **přehled** karta obsahuje seznam všech doporučení zdroje dat chcete napravit. 
* Kliknutí na jednotlivé karty a zobrazte doporučení podle typu prostředku.

    > [!NOTE]
    > Další informace o šifrování úložiště najdete v tématu [Povolení šifrování účtu úložiště Azure v Azure Security Center](security-center-enable-encryption-for-storage-account.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Opravovat doporučení na zdroj dat

1. Z některého z karty prostředků klikněte na prostředek. Otevře se stránka informace o seznam doporučení, která napravit.

    ![Informace o prostředku.](./media/security-center-monitoring/sql-recommendations.png)

2. Kliknutím na doporučení. Stránka doporučení se otevře a zobrazí **kroky k nápravě** implementovat doporučení.

   ![Kroky pro odstranění problému](./media/security-center-monitoring/remediate1.png)

3. Klikněte na tlačítko **provést akci**. Zobrazí se stránka nastavení prostředků.

    ![Povolte doporučení](./media/security-center-monitoring/remediate2.png)

4. Postupujte podle **kroky k nápravě** a klikněte na tlačítko **Uložit**.

## <a name="data-and-storage-recommendations"></a>Doporučení pro data a úložiště

|Typ prostředku|Bezpečnostní skóre|Doporučení|Popis|
|----|----|----|----|
|Účet úložiště|20|Měla by se povolit zabezpečený přenos pro účty úložiště|Zabezpečený přenos je možnost, která vynutí účtu úložiště tak, aby přijímal požadavky jenom ze zabezpečeného připojení (HTTPS). HTTPS zajišťuje ověřování mezi serverem a služby a chrání přenášená data ze sítě vrstvy útoky man-in-the-middle, odposlouchávání a napadení relace.|
|Redis|20|By měla být povolená pouze zabezpečená připojení k mezipaměti Redis|Povolte pouze připojení přes protokol SSL pro mezipaměť Azure Redis. Použití zabezpečeného připojení zajišťuje ověřování mezi serverem a služby a chrání přenášená data ze sítě vrstvy útoky man-in-the-middle, odposlouchávání a napadení relace.|
|SQL|15|Měla by se povolit transparentní šifrování dat v databázích SQL|Povolte transparentní šifrování dat pro ochranu dat v klidovém stavu a splňovat požadavky na dodržování předpisů.|
|SQL|15|Auditování služby SQL server by měla být povolená|Povolte auditování pro servery Azure SQL. (Pouze služby azure SQL. Nezahrnuje běžící na virtuálních počítačích SQL.)|
|Data lake analytics|5|By měly být povolené diagnostické protokoly v Data Lake Analytics|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Data lake store|5|Protokoly diagnostiky v Azure Data Lake Store by měly být povolené.|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|SQL|30|Měli napravit ohrožení zabezpečení na vašich databází SQL|Posouzení ohrožení zabezpečení SQL vyhledá vaší databáze na ohrožení zabezpečení a zpřístupňuje všechny odchylky od osvědčených postupů, jako jsou chybné konfigurace, nadměrná oprávnění a nechráněné citlivá data. Řešení zjištěná ohrožení zabezpečení může výrazně zlepšit vaše zásadní roli zabezpečení databáze.|
|SQL|20|Zřízení správce Azure AD pro SQL server|Zřízení správce Azure AD pro SQL server povolit ověřování Azure AD. Ověřování Azure AD umožňuje správu oprávnění zjednodušené a centralizované identity management uživatelů databáze a dalším službám společnosti Microsoft.|
|Účet úložiště|15|Přístup k účtům úložišť pomocí brány firewall a konfigurací virtuální sítě by mělo být omezeno|Audit neomezený přístup k síti v nastavení brány firewall na účtu úložiště. Místo toho nakonfigurujte pravidla sítě, abyste účtu úložiště můžou přistupovat jenom aplikace z povolených sítí. Povolit připojení z konkrétní Internet nebo klienty na místě, můžete udělit přístup k provoz z konkrétní služby Azure virtual networks a rozsahy adres veřejné internetové IP.|
|Účet úložiště|1|Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manageru|Použití nové verze 2 Azure Resource Manageru pro vaše účty úložiště kvůli vylepšení zabezpečení: silnější access control (RBAC), lepší auditování, nasazení založené na Resource Manageru a zásad správného řízení, přístup ke spravovaným identitám přístup k trezoru klíčů pro tajné kódy a Azure AD na základě ověřování a podpora značek a skupiny prostředků pro snadnější správu zabezpečení.|

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, najdete v následujících tématech:

* [Ochrana virtuálních počítačů pomocí Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrana aplikací pomocí Azure Security Center](security-center-application-recommendations.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)

Další informace o službě Security Center, najdete v následujících tématech:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
