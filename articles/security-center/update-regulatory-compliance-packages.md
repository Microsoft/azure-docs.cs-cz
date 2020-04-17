---
title: Jak aktualizovat na dynamické monitorování dodržování předpisů v centru zabezpečení Azure panelu dodržování předpisů | Dokumenty společnosti Microsoft
description: Aktualizace balíčků dodržování předpisů
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: fa5027ed285456247891c84e559b74a14237f553
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537776"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard"></a>Aktualizace dynamických balíčků dodržování předpisů na řídicím panelu dodržování předpisů

Azure Security Center průběžně porovnává konfiguraci vašich prostředků s požadavky v oborových standardech, předpisech a srovnávacích testech. **Řídicí panel dodržování předpisů** poskytuje přehled o stavu dodržování předpisů na základě toho, jak splňujete konkrétní kontroly dodržování předpisů a požadavky.

Jedním standardem, pro který můžete sledovat stav dodržování předpisů, je [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (formálněji "BENCHMARK CIS Microsoft Azure Foundations verze 1.1.0"). 

Reprezentace Azure CIS, která se zpočátku zobrazí v řídicím panelu dodržování předpisů, závisí na statické sadě pravidel, která je součástí Centra zabezpečení.

Díky funkci **dynamických balíčků dodržování předpisů** security center automaticky vylepšuje pokrytí oborových standardů v průběhu času. Balíčky dodržování předpisů jsou v podstatě iniciativy definované v Zásadách Azure. Lze je přiřadit k vybranému oboru (předplatné, skupina pro správu a tak dále). Pokud chcete na řídicím panelu zobrazit data o dodržování předpisů namapovaná jako hodnocení, přidejte balíček dodržování předpisů do skupiny pro správu nebo předplatného v rámci zásad zabezpečení. Přidání balíčku předpisů efektivně přiřadí iniciativu dodržování předpisů do vybraného oboru. Tímto způsobem můžete sledovat nově publikované regulační iniciativy jako standardy dodržování předpisů na řídicím panelu. Když společnost Microsoft vydá nový obsah pro iniciativu (nové zásady, které jsou mapovány na více ovládacích prvků ve standardu), další obsah se zobrazí automaticky na řídicím panelu.

Balíček dynamického dodržování předpisů pro srovnávací test Azure CIS Azure **CIS 1.1.0 (nový)** vylepšuje původní *statickou* verzi:

* Včetně dalších politik
* Automatická aktualizace s novým pokrytím při jeho přidání 

Aktualizujte na nový dynamický balíček, jak je popsáno níže.

## <a name="adding-a-dynamic-compliance-package"></a>Přidání dynamického balíčku dodržování předpisů

Následující kroky vysvětlují, jak přidat dynamický balíček pro sledování dodržování vašeho standardu Azure CIS v1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Aktualizace balíčku dynamického dodržování předpisů Azure CIS 1.1.0 (nový) 

1. Otevřete stránku **zásad zabezpečení.** Na této stránce je uveden počet skupin pro správu, předplatných, pracovních prostorů a struktury skupinpro správu.

1. Vyberte předplatné nebo skupinu pro správu, pro kterou chcete spravovat stav dodržování předpisů. Doporučujeme vybrat nejvyšší obor, pro který je standard použitelný, aby byla data dodržování předpisů agregována a sledována pro všechny vnořené prostředky. 

1. V části Obor & regulační standardy uvidíte, že Azure CIS 1.1.0 lze aktualizovat pro nový obsah. Klepněte na tlačítko **Aktualizovat .** 

1. Kliknutím na **Přidat další standardy** můžete navíc otevřít stránku **Přidat standardy dodržování předpisů.** Zde můžete ručně vyhledat **Azure CIS 1.1.0 (Nový)** a dynamické balíčky pro další standardy dodržování předpisů, jako je **NIST SP 800-53 R4**, **SWIFT CSP CSCF-v2020**, **UKO a UK NHS**a Kanada **PBMM**.
    
    > [!TIP]
    > K přidání standardů dodržování předpisů mají potřebná oprávnění pouze uživatelé, kteří jsou vlastníkem nebo přispěvatelem zásad. 

    ![Přidání regulačních balíčků na řídicí panel Centra pro dodržování předpisů azure security centra](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. V postranním panelu Centra zabezpečení vyberte **dodržování předpisů** a otevřete řídicí panel dodržování předpisů. 
    * Azure CIS 1.1.0 (Nový) se teď zobrazí v seznamu oborových & regulační standardy. 
    * Původní *statické* zobrazení dodržování předpisů Azure CIS 1.1.0 zůstane vedle něj. To může být automaticky odstraněny v budoucnu.

    > [!NOTE]
    > Může trvat několik hodin, než se nově přidaný standard zobrazí na řídicím panelu s předpisy.


    [![Řídicí panel dodržování předpisů zobrazující staré a nové služby Azure CIS](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli:

* Jak **upgradovat standardy** uvedené na řídicím panelu s předpisy na nové *dynamické* balíčky
* Jak **přidat balíčky dodržování předpisů** pro sledování dodržování dalších standardů. 

Další související materiály naleznete v následujících článcích: 

- [Řídicí panel dodržování předpisů centra zabezpečení](security-center-compliance-dashboard.md)
- [Práce se zásadami zabezpečení](tutorial-security-policy.md)
- [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak používat doporučení v Azure Security Center k ochraně prostředků Azure.