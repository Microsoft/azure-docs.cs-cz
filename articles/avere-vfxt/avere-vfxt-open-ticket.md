---
title: Jak získat podporu pro Avere vFXT pro Azure
description: Vysvětlení otevření lístků podpory o Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252555"
---
# <a name="get-help-with-your-system"></a>Získejte pomoc se svým systémem

Pokud chcete pomoct se systémem Avere vFXT for Azure, tady jsou způsoby, jak získat podporu:

* **Avere vFXT problém** – Použijte portál Azure otevřít lístek podpory pro avere vFXT, jak je popsáno [níže](#open-a-support-ticket-for-your-avere-vfxt).
* **Kvóta** – pokud máte problém související s kvótou, [požádejte o zvýšení kvóty](#request-a-quota-increase)
* **Dokumentace a příklady** – Pokud zjistíte problémy s touto dokumentací nebo příklady, přejděte na konec stránky s problémem a použijte část **Zpětná vazba** k vyhledání existujících problémů a v případě potřeby podejte nový.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Otevřete lístek podpory pro avere vFXT

Pokud narazíte na problémy při nasazování nebo používání Avere vFXT, požádejte o pomoc prostřednictvím portálu Azure.

Podle těchto kroků se ujistěte, že je lístek podpory označen prostředkem z vašeho clusteru. Označení lístku nám pomáhá směrovat jej ke správnému prostředku podpory.

1. Od [https://portal.azure.com](https://portal.azure.com)možnosti Od **položky Skupiny prostředků**vyberte . Přejděte do skupiny prostředků, která obsahuje cluster vFXT, kde k problému došlo, a klikněte na jeden z virtuálních počítačů clusteru Avere.

    ![snímek obrazovky panelu "přehled" skupiny prostředků portálu Azure portal s určitým virtuálním počítačem zakroužkovaným](media/avere-vfxt-ticket-vm.png)

1. Na stránce Virtuální ho virtuálního okraje přejděte dolů do dolní části levého panelu a klikněte na **Nová žádost o podporu**.

    ![Snímek obrazovky stránky virtuálního počítače portálu Azure pro virtuální počítač z předchozího snímku obrazovky V levé nabídce se posunete do dolní části a "Nová žádost o podporu" je zakroužkována.](media/avere-vfxt-ticket-request.png)

1. Na první stránce žádosti o podporu zvolte typ problému a ujistěte se, že je vybráno správné předplatné.

   V části **Služba**klikněte na **Všechny služby** a v části **Úložiště** vyberte **Avere vFXT**.

   Přidejte krátký souhrn a vyberte typ problému.

    ![snímek obrazovky s novou obrazovkou žádostí o podporu na webu Azure Portal. Je vybrána karta Základy. Položky obrazovky zahrnují typ problému, předplatné, službu, souhrn a typ problému.](media/ticket-basics.png)

   Pokračujte výběrem tlačítka **Další**.

1. Druhá stránka formuláře podpory obsahuje návrhy na řešení problému na základě souhrnného popisu. Pokud stále potřebujete vytvořit lístek podpory, klikněte v dolní části na tlačítko **Další.**

   ![snímek obrazovky nové žádosti o podporu s vybranou kartou Řešení Textové pole uprostřed má název "Doporučené řešení" a vysvětluje možné opravné prostředky.](media/ticket-solutions.png)

1. Na třetí stránce uveďte podrobnosti – to zahrnuje informace o clusteru, čas, kdy k problému došlo, závažnost a jak vás kontaktovat. Vyplňte informace a klikněte na tlačítko **Další** dole.

   ![snímek obrazovky nové žádosti o podporu s vybranou kartou Podrobnosti. Informační pole jsou uspořádána do kategorií Podrobnosti o problému, "Metoda podpory" a Kontaktní údaje.](media/ticket-details.png)

1. Zkontrolujte informace na poslední stránce a klepněte na tlačítko **Vytvořit**. Na vaši e-mailovou adresu bude zasláno potvrzení a číslo letenky a bude vás kontaktovat pracovník podpory.

## <a name="request-a-quota-increase"></a>Žádost o zvýšení kvóty

Přečtěte si [kvótu pro cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) a zjistěte, jaké součásti jsou potřeba k nasazení Avere vFXT pro Azure. Můžete [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) z webu Azure Portal.
