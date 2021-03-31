---
title: Jak získat podporu pro avere vFXT pro Azure
description: Naučte se řešit problémy, které mohou nastat při nasazení nebo používání avere vFXT pro Azure, vytvořením lístku podpory prostřednictvím Azure Portal.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 8159d83624cdd474d91030d5376a3db447beffe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008425"
---
# <a name="get-help-with-your-system"></a>Získejte pomoc s vaším systémem.

Pokud potřebujete pomoc s avere vFXT pro systém Azure, tady jsou způsoby, jak získat podporu:

* **Avere vFXT problém** – pomocí Azure Portal otevřete lístek podpory pro avere vFXT, jak je popsáno [níže](#open-a-support-ticket-for-your-avere-vfxt).
* **Kvóta** – Pokud máte problém související s kvótou, [požádejte o zvýšení kvóty](#request-a-quota-increase) .
* **Dokumentace a příklady** – Pokud najdete problémy s touto dokumentací nebo příklady, posuňte se k dolnímu okraji stránky a v případě potřeby můžete v části **Zpětná vazba** vyhledat existující problémy a vytvořit nové soubory.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Otevření lístku podpory pro avere vFXT

Pokud narazíte na problémy při nasazování nebo používání avere vFXT, požádejte o technickou podporu Azure Portal.

Pomocí těchto kroků se ujistěte, že je váš lístek podpory označený prostředkem z vašeho clusteru. Označení lístku vám pomůže nám ho směrovat na správný prostředek podpory.

1. V nástroji [https://portal.azure.com](https://portal.azure.com) Vyberte **skupiny prostředků**. Přejděte do skupiny prostředků, která obsahuje cluster vFXT, ve kterém došlo k problému, a klikněte na jeden z virtuálních počítačů clusteru avere.

    ![snímek obrazovky s Azure Portalou skupinou prostředků "Přehled" s konkrétním virtuálním počítačem v kruhu](media/avere-vfxt-ticket-vm.png)

1. Na stránce virtuální počítač přejděte dolů k dolnímu okraji levého panelu a klikněte na **Nová žádost o podporu**.

    ![Snímek obrazovky Azure Portalho virtuálního počítače pro virtuální počítač z předchozího snímku obrazovky V levé nabídce se posune dolů a na nový požadavek na podporu se zakroužkuje.](media/avere-vfxt-ticket-request.png)

1. Na první stránce žádosti o podporu vyberte typ problému a ujistěte se, že je vybrané správné předplatné.

   V části **Služba** klikněte na **všechny služby** a v části **úložiště** vyberte **avere vFXT**.

   Přidejte krátký souhrn a vyberte typ problému.

    ![snímek obrazovky s novou žádostí o podporu v Azure Portal. Je vybrána karta základy. Položky obrazovky zahrnují typ problému, předplatné, službu, souhrn a typ problému.](media/ticket-basics.png)

   Pokračujte kliknutím na **Next** (Další).

1. Druhá stránka formuláře podpory obsahuje návrhy pro vyřešení problému na základě vašeho souhrnného popisu. Pokud stále potřebujete vytvořit lístek podpory, klikněte na tlačítko **Další** v dolní části.

   ![snímek obrazovky nové žádosti o podporu se zvolenou kartou řešení Textové pole uprostřed má název doporučené řešení a vysvětluje možné nápravy.](media/ticket-solutions.png)

1. Na třetí stránce zadejte podrobnosti – obsahuje informace o vašem clusteru, čas výskytu problému, závažnost a způsob, jak vás kontaktovat. Vyplňte informace a klikněte na tlačítko **Další** v dolní části.

   ![snímek obrazovky nové žádosti o podporu se zvolenou kartou podrobnosti Informační pole jsou uspořádána do kategorií "Detaily problému", "metoda podpory" a "kontaktní údaje".](media/ticket-details.png)

1. Zkontrolujte informace na poslední stránce a klikněte na **vytvořit**. Na vaši e-mailovou adresu se pošle potvrzení a číslo lístku, na které vás bude kontaktovat člen zaměstnance podpory.

## <a name="request-a-quota-increase"></a>Požádat o zvýšení kvóty

Přečtěte si [kvótu pro cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) , kde se dozvíte, jaké součásti jsou potřeba k nasazení avere VFXT pro Azure. Z Azure Portal můžete [požádat o zvýšení kvóty](../azure-portal/supportability/resource-manager-core-quotas-request.md) .