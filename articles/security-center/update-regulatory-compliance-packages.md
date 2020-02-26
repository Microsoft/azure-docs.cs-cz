---
title: Postup aktualizace na monitorování dodržování předpisů na dynamické předpisy ve Azure Security Center řídicím panelu dodržování předpisů Microsoft Docs
description: Aktualizují se balíčky pro dodržování předpisů (Preview).
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
ms.openlocfilehash: cfa39799e44cee0a2d36efccd454ccf8ca15157f
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603229"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>Aktualizace na dynamické balíčky dodržování předpisů na řídicím panelu dodržování předpisů (Preview)

Azure Security Center průběžně porovnává konfiguraci vašich prostředků s požadavky v oborových normách, nařízeních a srovnávacích testech. **Řídicí panel dodržování předpisů** poskytuje přehledy o stav dodržování předpisů na základě toho, jak splňujete konkrétní kontrolu a požadavky na dodržování předpisů.

Jedním z standardů, pro které můžete sledovat stav dodržování předpisů, je [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (ve verzi "CIS Microsoft Azure Foundation test Version 1.1.0"). 

Reprezentace Azure CIS, která se zpočátku zobrazuje na řídicím panelu dodržování předpisů, spoléhá na statickou sadu pravidel, která je součástí Security Center.

Díky funkci **dynamické balíčky s kompatibilitou (Preview)** Security Center automaticky zlepšuje pokrytí oborových standardů v průběhu času. Balíčky dodržování předpisů jsou v podstatě iniciativy definované v Azure Policy. Dají se přiřadit k vybranému oboru (předplatné, skupina pro správu atd.). Pokud chcete zobrazit data o dodržování předpisů namapovaná jako posouzení na řídicím panelu, přidejte do skupiny pro správu nebo předplatného ze zásad zabezpečení balíček pro dodržování předpisů. Přidáním balíčku pro dodržování předpisů se efektivně přiřadí iniciativa dodržování předpisů pro vybraný obor. Tímto způsobem můžete na řídicím panelu sledovat nově publikované legislativní iniciativy jako standardy dodržování předpisů. Když společnost Microsoft vydává nový obsah pro iniciativu (nové zásady, které se mapují na více ovládacích prvků standardně), zobrazí se na řídicím panelu automaticky tento další obsah.

Balíček dynamické kompatibility pro Azure CIS test, **Azure CIS 1.1.0 (novinka)** , vylepšuje původní *statickou* verzi:

* Zahrnutí dalších zásad
* Automatická aktualizace s novým pokrytím při jeho přidání 

Aktualizujte na nový dynamický balíček, jak je popsáno níže.

## <a name="adding-a-dynamic-compliance-package"></a>Přidání dynamického balíčku pro dodržování předpisů

Následující postup vysvětluje, jak přidat dynamický balíček pro monitorování dodržování předpisů pomocí testu výkonnosti Azure CIS v 1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Aktualizace na Azure SNS 1.1.0 (nový) dynamický balíček pro dodržování předpisů 

1. Otevřete stránku **zásady zabezpečení** . Tato stránka zobrazuje počet skupin pro správu, předplatných, pracovních prostorů a struktury skupin pro správu.

1. Vyberte předplatné nebo skupinu pro správu, pro které chcete spravovat dodržování legislativních předpisů stav. Doporučujeme vybrat nejvyšší rozsah, pro který je standard použitelný, aby se data o dodržování předpisů agreguje a sledovala pro všechny vnořené prostředky. 

1. V části obor & regulativních standardů (Preview) uvidíte, že Azure CIS 1.1.0 se dá aktualizovat pro nový obsah. Klikněte na **aktualizovat hned**. 

1. Volitelně můžete kliknutím na **Přidat další standardy** otevřít stránku **Přidat standardy dodržování předpisů** . Tady můžete vyhledávat ručně pro **Azure CIS 1.1.0 (nové)** a dynamické balíčky pro jiné standardy dodržování předpisů, jako je **NIST SP 800-53 R4**, **SWIFT CSP CSCF-V2020**, **UKO a UK NHS**a **Kanada PBMM**.
    
    ![Přidání regulativních balíčků do řídicího panelu dodržování předpisů pro Azure Security Center](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Z bočního panelu Security Center vyberte **dodržování předpisů** pro otevření řídicího panelu dodržování předpisů regulativním předpisem. 
    * Azure CIS 1.1.0 (nové) se teď zobrazí v seznamu oborových & regulativních standardů. 
    * Původní *statické* zobrazení dodržování předpisů ve službě Azure CIS 1.1.0 zůstane zároveň současně. V budoucnu může být automaticky odebrána.

    > [!NOTE]
    > Může trvat několik hodin, než se nově přidaný Standard zobrazí na řídicím panelu dodržování předpisů.


    [řídicí panel dodržování předpisů ![, který ukazuje starou a novou službu Azure SNS](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili:

* Postup **upgradu standardů** zobrazených na řídicím panelu dodržování předpisů pro nové *dynamické* balíčky
* Postup **Přidání balíčků dodržování předpisů** pro monitorování dodržování předpisů pomocí dalších standardů. 

Další související materiály najdete v následujících článcích: 

- [Řídicí panel dodržování legislativních předpisů služby Security Center](security-center-compliance-dashboard.md)
- [Práce se zásadami zabezpečení](tutorial-security-policy.md)
- [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak používat doporučení v Azure Security Center k ochraně vašich prostředků Azure.