---
title: Ochrana služby Azure data a úložiště v Azure Security Center | Microsoft Docs
description: Tento dokument popisuje doporučení v Azure Security Center, která vám pomůžou chránit vaše data a službu Azure SQL a zůstat v souladu se zásadami zabezpečení.
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
ms.author: v-mohabe
ms.openlocfilehash: 0704db7444756fb44c5587ae8e8e743f671c9dcc
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907944"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Ochrana služby Azure data a úložiště v Azure Security Center
V tomto tématu se dozvíte, jak zobrazit a implementovat doporučení zabezpečení pro prostředky dat a úložiště. Azure Security Center při analýze stavu zabezpečení vašich prostředků Azure našla tato doporučení.

## <a name="view-your-data-security-information"></a>Zobrazit informace o zabezpečení dat

1. V části **hygiena zabezpečení prostředků** klikněte na **data a prostředky úložiště**.

   ![Prostředky úložiště dat &](./media/security-center-monitoring/click-data.png)

    Otevře se stránka **zabezpečení dat** s doporučeními pro datové prostředky.

     ![Prostředky dat](./media/security-center-monitoring/sql-overview.png)

Na této stránce můžete:

* Klikněte na kartu **Přehled** se seznamem všech doporučení k datovým prostředkům, která se mají opravit. 
* Klikněte na jednotlivé karty a Prohlédněte si doporučení podle typu prostředku.

    > [!NOTE]
    > Další informace o šifrování úložiště najdete v tématu [Azure Storage Encryption for data v klidovém umístění](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Náprava doporučení pro datový prostředek

1. Ze všech karet prostředků klikněte na prostředek. Otevře se stránka s informacemi o doporučeních, která se mají opravit.

    ![Informace o prostředku](./media/security-center-monitoring/sql-recommendations.png)

2. Klikněte na doporučení. Otevře se stránka s doporučením a zobrazí se **kroky** pro nápravu, které implementují doporučení.

   ![Kroky pro odstranění problému](./media/security-center-monitoring/remediate1.png)

3. Klikněte na **provést akci**. Zobrazí se stránka nastavení prostředků.

    ![Povolit doporučení](./media/security-center-monitoring/remediate2.png)

4. Postupujte podle **kroků** pro nápravu a klikněte na **Uložit**.

## <a name="data-and-storage-recommendations"></a>Doporučení pro data a úložiště

|Typ prostředku|Bezpečnostní skóre|Doporučení|Popis|
|----|----|----|----|
|Účet úložiště|20|Zabezpečený přenos do účtů úložiště by měl být povolený.|Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Protokol HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například útoky prostředníkem, odposlouchávání a napadení relace.|
|Redis|20|Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.|Povolte pro Redis jenom připojení přes SSL k Azure cache. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například útoky prostředníkem, odposlouchávání a napadení relace.|
|SQL|15|Je třeba povolit transparentní šifrování dat databází SQL.|Povolit transparentní šifrování dat pro ochranu neaktivních dat a splnění požadavků na dodržování předpisů.|
|SQL|15|Auditování SQL serveru by mělo být povolené.|Povolte auditování pro servery SQL Azure. (Jenom Azure SQL Service. Nezahrnuje SQL běžící na vašich virtuálních počítačích.)|
|Data Lake Analytics|5|Měly by být povolené diagnostické protokoly v Data Lake Analytics.|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Data Lake Store|5|Měly by být povolené diagnostické protokoly v Azure Data Lake Store.|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|SQL|30|Ohrožení zabezpečení vašich databází SQL by mělo být opraveno|Posouzení ohrožení zabezpečení SQL prohledává chyby zabezpečení v databázi a zpřístupňuje jakékoli odchylky od osvědčených postupů, jako jsou například chyby konfigurace, nadměrné oprávnění a nechráněná citlivá data. Řešení zjištěných slabých míst může významně zlepšit stature zabezpečení databáze.|
|SQL|20|Zřízení správce Azure AD pro SQL Server|Zřiďte správce Azure AD pro SQL Server, aby se povolilo ověřování Azure AD. Ověřování Azure AD umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu.|
|Účet úložiště|15|Přístup k účtům úložiště pomocí brány firewall a konfigurací virtuální sítě by měl být omezený.|Auditujte neomezený přístup k síti v nastavení brány firewall účtu úložiště. Místo toho nakonfigurujte síťová pravidla, aby k účtu úložiště měly přístup jenom aplikace z povolených sítí. Pokud chcete povolit připojení z určitých internetových nebo místních klientů, můžete udělit přístup k provozu z konkrétních virtuálních sítí Azure nebo do rozsahů veřejných internetových IP adres.|
|Účet úložiště|1|Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.|Použijte novou Azure Resource Manager V2 pro účty úložiště k zajištění vylepšení zabezpečení, jako je: silnější řízení přístupu (RBAC), lepší auditování, nasazení Správce prostředků a zásady správného řízení, přístup ke spravovaným identitám, přístup k trezoru klíčů pro tajné kódy a ověřování založené na službě Azure AD a podpora pro značky a skupiny prostředků pro snadnější správu zabezpečení.|

## <a name="see-also"></a>Viz také:
Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujících tématech:

* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrana aplikací pomocí Azure Security Center](security-center-application-recommendations.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)

Další informace o Security Center najdete v následujících tématech:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
