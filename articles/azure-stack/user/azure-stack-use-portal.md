---
title: "Pomocí portálu Azure zásobníku | Microsoft Docs"
description: "Zjistěte, jak přístup a používání portálu user portal v Azure zásobníku."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 7c34d7a225be63da95f664525b0366ff89b28838
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="using-the-azure-stack-portal"></a>Pomocí portálu Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Jako příjemce služeb Azure zásobníku můžete pomocí portálu Azure zásobníku k odběru veřejné nabídky a používat služby, které jsou k dispozici prostřednictvím těchto nabídek. Pokud jste použili portál Azure před, jste již obeznámeni s uživatelským rozhraním.

## <a name="access-the-portal"></a>Přístup k portálu

Vaše zásobník Azure – operátor (poskytovatele služeb nebo správce ve vaší organizaci), vám poradí správnou adresu URL pro přístup k portálu. 

- Pro integrovaný systém adresa URL se liší v závislosti na vaší operátor oblast a název externí domény a bude v https://portal formátu. &lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
- Pokud používáte Development Kit Azure zásobníku, portál adresa je https://portal.local.azurestack.external.

![Snímek obrazovky portálu Azure zásobník uživatele](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel

Řídicí panel obsahuje sadu výchozích dlaždic. Můžete kliknout na **úprava řídicího panelu** změnit výchozí řídicí panely, nebo klikněte na tlačítko **novým řídicím panelem** přidat vlastní řídicí panely. Dlaždice můžete snadno přidat na řídicí panel. Například můžete kliknout na **nový**, klikněte pravým tlačítkem na **výpočetní**a potom klikněte na **připnout na řídicí panel**.

## <a name="create-subscription-and-browse-available-resources"></a>Vytvoření odběru a přejděte k dispozici prostředky
 
Pokud ještě nemáte předplatné, je první věcí, kterou je potřeba udělat, přihlášení k odběru na nabídku. Potom můžete vyhledat prostředky, ke kterým jsou k dispozici. Chcete-li procházet a vytvořit prostředky, provádět žádnou z následujících akcí:

- Klikněte **Marketplace** dlaždici na řídicím panelu. 
- Na **všechny prostředky** dlaždici, klikněte na tlačítko **vytvořit prostředky**.
- V levém navigačním podokně klikněte na tlačítko **nový**.

## <a name="learn-how-to-use-available-services"></a>Další informace o použití služby k dispozici

Pokud budete potřebovat pokyny, jak používat služby k dispozici, může mít různé možnosti k dispozici.

- Vaše organizace nebo poskytovatel služeb mohou poskytnout vlastní dokumentace. To platí hlavně pokud nabízejí vlastní služby nebo aplikace.
- Aplikace třetích stran mají vlastní dokumentace.
- Pro služby Azure konzistentní důrazně doporučujeme nejprve zkontrolujte v dokumentaci k Azure zásobníku. Pro přístup k dokumentaci pro uživatele Azure zásobníku, klikněte na ikonu nápovědy a pak klikněte na tlačítko **Nápověda a podpora**.
 
    ![Snímek obrazovky možnosti nápovědy a podpory v uživatelském rozhraní](media/azure-stack-use-portal/HelpAndSupport.png)

    Zejména doporučujeme, abyste si prošli Začínáme v těchto článcích:

    - [Klíčové aspekty: pomocí služby nebo vytváření aplikací pro Azure zásobníku](azure-stack-considerations.md)
    - V části "Použití služby" dokumentace se zobrazí každý Azure konzistentní službu uvedené. Je téma "požadavky" pro každou službu, která popisuje rozdíly mezi službou nabízí v Azure a stejnou službu nabízí v zásobníku Azure. Příklad, naleznete v části [aspekty virtuálních počítačů](azure-stack-vm-considerations.md). Mohou existovat další informace v části "Použití služby", které jsou jedinečné pro Azure zásobníku. 
     
      Dokumentace k Azure můžete použít jako obecné referenční informace pro služby, ale je potřeba vědět, tyto rozdíly. Pochopit, že dokumentace odkazuje na **rychlý start kurzy** dlaždici bod do dokumentace k Azure.

## <a name="get-support"></a>Získat podporu

Pokud potřebujete pomoc, požádejte o pomoc vaše organizace nebo služba poskytovatele. 

Pokud používáte Azure zásobníku Development Kit, [fórum Azure zásobníku](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) je jediným způsobem, jak podpory.

## <a name="next-steps"></a>Další postup

[Klíčové aspekty: pomocí služby nebo vytváření aplikací pro Azure zásobníku](azure-stack-considerations.md)
