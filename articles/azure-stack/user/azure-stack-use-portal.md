---
title: Pomocí portálu Azure zásobníku | Microsoft Docs
description: Zjistěte, jak přístup a používání portálu user portal v Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 2ebafc4249f8455bdbe45a07a5bf88aa8984d67a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="using-the-azure-stack-portal"></a>Pomocí portálu Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Můžete pomocí portálu Azure zásobníku k odběru veřejné nabídky a použití služeb, které poskytují tyto nabídky. Pokud jste použili globální portál Azure, jste již obeznámeni s fungování lokality.

## <a name="access-the-portal"></a>Přístup k portálu

Vaše zásobník Azure – operátor (poskytovatele služeb nebo správce ve vaší organizaci), vám poradí správnou adresu URL pro přístup k portálu.

- Pro integrovaný systém, se liší v závislosti na vaší operátor oblast a název domény externí adresu URL a bude ve formátu https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
- Pokud používáte Development Kit Azure zásobníku, portál adresa je https://portal.local.azurestack.external.

![Snímek obrazovky portálu Azure zásobník uživatele](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel

Řídicí panel obsahuje sadu výchozích dlaždic. Můžete kliknout na **úprava řídicího panelu** změnit výchozí řídicí panely, nebo klikněte na tlačítko **novým řídicím panelem** vytvořit vlastní řídicí panel. Řídicí panel můžete snadno přizpůsobit přidáním nebo odebráním dlaždice. Například pokud chcete přidat výpočetní dlaždici, klikněte na tlačítko **nový**. Klikněte pravým tlačítkem na **výpočetní**a potom klikněte na **připnout na řídicí panel**.

## <a name="create-subscription-and-browse-available-resources"></a>Vytvoření odběru a přejděte k dispozici prostředky
 
Pokud ještě nemáte předplatné, je první věcí, kterou je potřeba udělat, přihlášení k odběru na nabídku. Potom můžete procházet dostupné prostředky. Pokud chcete procházet a vytvářet prostředky, použijte některou z následujících postupů:

- Klikněte **Marketplace** dlaždici na řídicím panelu.
- Na **všechny prostředky** dlaždici, klikněte na tlačítko **vytvořit prostředky**.
- V levém navigačním podokně klikněte na tlačítko **nový**.

## <a name="learn-how-to-use-available-services"></a>Další informace o použití služby k dispozici

Pokud budete potřebovat pokyny, jak používat služby k dispozici, může mít různé možnosti k dispozici.

- Vaše organizace nebo poskytovatel služeb mohou poskytnout vlastní dokumentace, která se obvykle stává, pokud nabízejí vlastní služby nebo aplikace.
- Aplikace třetích stran mají vlastní dokumentace.
- Pro služby Azure konzistentní důrazně doporučujeme nejprve zkontrolujte v dokumentaci k Azure zásobníku. Pro přístup k dokumentaci pro uživatele Azure zásobníku, klikněte na ikonu nápovědy a pak klikněte na tlačítko **Nápověda a podpora**.
 
    ![Snímek obrazovky možnosti nápovědy a podpory v uživatelském rozhraní](media/azure-stack-use-portal/HelpAndSupport.png)

    Zejména doporučujeme, abyste si prošli Začínáme v těchto článcích:

    - [Klíčové aspekty: pomocí služby nebo vytváření aplikací pro Azure zásobníku](azure-stack-considerations.md)
    - V **použití služeb** části dokumentace, je článek důležité informace pro každou službu. Aspekty stránka popisuje rozdíly mezi službou nabízí v Azure a stejnou službu nabízí v zásobníku Azure. Příklad, naleznete v části [aspekty virtuálních počítačů](azure-stack-vm-considerations.md). Může obsahovat další informace **použití služeb** oddíl, který je jedinečné pro Azure zásobníku.
     
      Dokumentace k Azure můžete použít jako obecné referenční informace pro služby, ale je potřeba vědět, tyto rozdíly. Pochopit, že dokumentace odkazuje na **rychlý start kurzy** dlaždici bod do dokumentace k Azure.

## <a name="get-support"></a>Získat podporu

Pokud potřebujete podporu, požádejte o pomoc vaše organizace nebo služba poskytovatele.

Pokud používáte Azure zásobníku Development Kit, [fórum Azure zásobníku](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) je jediný zdroj podpory.

## <a name="next-steps"></a>Další postup

[Klíčové aspekty: pomocí služby nebo vytváření aplikací pro Azure zásobníku](azure-stack-considerations.md)
