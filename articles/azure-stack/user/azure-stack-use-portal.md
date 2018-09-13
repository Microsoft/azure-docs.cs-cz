---
title: Pomocí portálu Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak přistupovat a používat portál user portal v Azure stacku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 7ccf746cfd9645b63c2810bff83cfb0c4fa55d65
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713729"
---
# <a name="use-the-azure-stack-portal"></a>Použití portálu Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete si můžete předplatit na veřejné nabídky pomocí portálu Azure Stack a používat služby, které poskytují tyto nabídky. Pokud jste použili na webu Azure portal globální, jste již obeznámeni s fungování lokality.

## <a name="access-the-portal"></a>Přístup k portálu

Vaše služby Azure Stack – operátor (poskytovatel služeb nebo správce ve vaší organizaci), vám dá vědět správnou adresu URL pro přístup k portálu.

- Integrovaný systém, adresa URL se liší v závislosti na oblasti vaší operátor a název domény externího a bude mít tento formát https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
- Pokud používáte Azure Stack Development Kit, adresy portálu je https://portal.local.azurestack.external.

![Snímek obrazovky portálu user Portal Azure Stack](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Přizpůsobení řídicího panelu

Řídicí panel obsahuje sadu výchozích dlaždic. Můžete vybrat **upravit řídicí panel** změnit výchozí řídicí panel, nebo vyberte **nový řídicí panel** k vytvoření vlastního řídicího panelu. Řídicí panel můžete snadno přizpůsobit přidáním nebo odebráním dlaždice. Například pokud chcete přidat dlaždici výpočetní prostředky, vyberte **+ vytvořit prostředek**. Klikněte pravým tlačítkem na **Compute**a pak vyberte **připnout na řídicí panel**.

## <a name="create-subscription-and-browse-available-resources"></a>Vytvoření odběru a procházet dostupné prostředky.

Pokud ještě nemáte předplatné, jsou první, co musíte udělat, odběr na nabídku. Potom můžete procházet dostupné prostředky. Procházení a vytváření prostředků, použijte některou z následujících postupů:

- Vyberte **Marketplace** dlaždici na řídicím panelu.
- Na **všechny prostředky** dlaždice, vyberte **vytvářet prostředky**.
- V levém navigačním podokně vyberte **+ vytvořit prostředek**.

## <a name="learn-how-to-use-available-services"></a>Další informace o použití dostupných služeb

Pokud potřebujete pokyny, jak používat dostupné služby, můžou existovat různé možnosti k dispozici.

- Vaše organizace nebo poskytovatel služeb mohou poskytovat vlastní dokumentace, která je obvykle případ, nabízí vlastní služby nebo aplikace.
- Aplikace třetí strany mají své vlastní dokumentace.
- U služeb konzistentních s Azure důrazně doporučujeme, abyste si nejdřív dokumentace ke službě Azure Stack. Pro přístup k dokumentaci pro uživatele služby Azure Stack, vyberte ikonu nápovědy a pak vyberte **Nápověda a podpora**.

    ![Nápověda a podpora možnost v uživatelském rozhraní](media/azure-stack-use-portal/HelpAndSupport.png)

    Zejména doporučujeme, abyste si následující články, abyste mohli začít:

    - [Klíč aspekty: použití služeb nebo vytváření aplikací pro Azure Stack](azure-stack-considerations.md)
    - V **používat služby** části dokumentace ke službě, je článek důležité informace pro každou službu. Důležité informace o stránce popisuje rozdíly mezi service nabízí v Azure a ve stejné službě Azure Stack nabízí. Příklad najdete v tématu [aspekty virtuálních počítačů](azure-stack-vm-considerations.md). Může obsahovat další informace **používat služby** oddíl, který je jedinečný pro Azure Stack.

      Dokumentace k Azure můžete použít jako obecné referenční informace pro služby, ale je nutné znát tyto rozdíly. Vysvětlení, že dokumentace odkazuje na **kurzy rychlý Start** dlaždici, přejděte na dokumentaci k Azure.

## <a name="get-support"></a>Získat podporu

Pokud potřebujete podporu, požádejte o pomoc vaší organizace nebo poskytovali služeb.

Pokud používáte Azure Stack Development Kit, můžete [fórum pro Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) je jediný zdroj podpory.

## <a name="next-steps"></a>Další postup

[Klíč aspekty: použití služeb nebo vytváření aplikací pro Azure Stack](azure-stack-considerations.md)
