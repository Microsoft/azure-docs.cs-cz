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
ms.date: 06/01/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 7ca29ee359349f69c3d5ff21bd9db3f93358206a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724308"
---
# <a name="use-the-azure-stack-portal"></a>Použití portálu Azure Stack

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Můžete pomocí portálu Azure zásobníku k odběru veřejné nabídky a použití služeb, které poskytují tyto nabídky. Pokud jste použili globální portál Azure, jste již obeznámeni s fungování lokality.

## <a name="access-the-portal"></a>Přístup k portálu

Vaše zásobník Azure – operátor (poskytovatele služeb nebo správce ve vaší organizaci), vám poradí správnou adresu URL pro přístup k portálu.

- Pro integrovaný systém, se liší v závislosti na vaší operátor oblast a název domény externí adresu URL a bude ve formátu https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
- Pokud používáte Development Kit Azure zásobníku, portál adresa je https://portal.local.azurestack.external.

![Snímek obrazovky portálu user Portal Azure zásobníku](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel

Řídicí panel obsahuje sadu výchozích dlaždic. Můžete vybrat **úprava řídicího panelu** změnit výchozí řídicí panely, nebo vyberte **novým řídicím panelem** vytvořit vlastní řídicí panel. Řídicí panel můžete snadno přizpůsobit přidáním nebo odebráním dlaždice. Můžete například přidat výpočetní dlaždici, vybrat možnost **nový**. Klikněte pravým tlačítkem na **výpočetní**a potom vyberte **připnout na řídicí panel**.

## <a name="create-subscription-and-browse-available-resources"></a>Vytvoření odběru a přejděte k dispozici prostředky

Pokud ještě nemáte předplatné, je první věcí, kterou je potřeba udělat, přihlášení k odběru na nabídku. Potom můžete procházet dostupné prostředky. Pokud chcete procházet a vytvářet prostředky, použijte některou z následujících postupů:

- Vyberte **Marketplace** dlaždici na řídicím panelu.
- Na **všechny prostředky** dlaždice, vyberte **vytvořit prostředky**.
- V levém navigačním podokně vyberte **nový**.

## <a name="learn-how-to-use-available-services"></a>Další informace o použití služby k dispozici

Pokud budete potřebovat pokyny, jak používat služby k dispozici, může mít různé možnosti k dispozici.

- Vaše organizace nebo poskytovatel služeb mohou poskytnout vlastní dokumentace, která se obvykle stává, pokud nabízejí vlastní služby nebo aplikace.
- Aplikace třetích stran mají vlastní dokumentace.
- Pro služby Azure konzistentní důrazně doporučujeme nejprve zkontrolujte v dokumentaci k Azure zásobníku. Chcete-li získat přístup k dokumentaci pro uživatele Azure zásobníku, vyberte ikonu nápovědy a pak vyberte **Nápověda a podpora**.

    ![Nápověda a podpora možnost v uživatelském rozhraní](media/azure-stack-use-portal/HelpAndSupport.png)

    Zejména doporučujeme, abyste si prošli Začínáme v těchto článcích:

    - [Klíčové aspekty: pomocí služby nebo vytváření aplikací pro Azure zásobníku](azure-stack-considerations.md)
    - V **použití služeb** části dokumentace, je článek důležité informace pro každou službu. Aspekty stránka popisuje rozdíly mezi službou nabízí v Azure a stejnou službu nabízí v zásobníku Azure. Příklad, naleznete v části [aspekty virtuálních počítačů](azure-stack-vm-considerations.md). Může obsahovat další informace **použití služeb** oddíl, který je jedinečné pro Azure zásobníku.

      Dokumentace k Azure můžete použít jako obecné referenční informace pro služby, ale je potřeba vědět, tyto rozdíly. Pochopit, že dokumentace odkazuje na **rychlý start kurzy** dlaždici bod do dokumentace k Azure.

## <a name="get-support"></a>Získat podporu

Pokud potřebujete podporu, požádejte o pomoc vaše organizace nebo služba poskytovatele.

Pokud používáte Azure zásobníku Development Kit, [fórum Azure zásobníku](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) je jediný zdroj podpory.

## <a name="next-steps"></a>Další postup

[Klíčové aspekty: pomocí služby nebo vytváření aplikací pro Azure zásobníku](azure-stack-considerations.md)
