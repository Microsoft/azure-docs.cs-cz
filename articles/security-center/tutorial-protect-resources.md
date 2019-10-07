---
title: Kurz Azure Security Center – Ochrana prostředků pomocí Azure Security Center | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nakonfigurovat zásady přístupu k VIRTUÁLNÍm počítačům za běhu a zásadu řízení aplikací.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 8cb07f3447e50528a94811f33a2142086f698586
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996336"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Kurz: Ochrana prostředků pomocí Azure Security Center
Security Center omezuje vystavení hrozbám pomocí řízení přístupu a aplikací k blokování škodlivých aktivit. Přístup k virtuálnímu počítači za běhu (JIT) snižuje vaši expozici útokům tím, že vám umožní odepřít trvalý přístup k virtuálním počítačům. Místo toho zadáte řízený a auditovaný přístup k virtuálním počítačům pouze v případě potřeby. Adaptivní řízení aplikací umožňuje posílit virtuální počítače proti malwaru tím, že určuje, které aplikace se můžou na vašich virtuálních počítačích spouštět. Security Center využívá Machine Learning k analýze procesů spuštěných ve virtuálním počítači a pomůže vám s použitím tohoto inteligentního pravidla použít pravidla přidávání na seznam povolených.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace zásad přístupu k VIRTUÁLNÍm počítačům za běhu
> * Konfigurace zásad řízení aplikací

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Pro krokování s funkcemi popsanými v tomto kurzu musíte být v Security Center cenové úrovně Standard. Můžete vyzkoušet Security Center Standard bez jakýchkoli nákladů. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/security-center/). V tomto rychlém startu se v [předplatném Azure připojíte k Security Center úrovně Standard](security-center-get-started.md) , provedete to pomocí upgradu na standard.

## <a name="manage-vm-access"></a>Správa přístupu k virtuálnímu počítači
Přístup k virtuálnímu počítači JIT se dá použít k uzamknutí příchozího provozu do virtuálních počítačů Azure. tím se sníží riziko útoků na útoky a zároveň se v případě potřeby zajistí snadný přístup k virtuálním počítačům.

Porty pro správu není nutné otevírat po všech časech. Stačí je otevřít, jenom když jste připojeni k virtuálnímu počítači, například provádět úlohy správy nebo údržby. Pokud je povolený program just-in-time, Security Center používá pravidla skupiny zabezpečení sítě (NSG), která omezují přístup k portům pro správu, aby na ně nedokázali zacílit útočníci.

1. V hlavní nabídce Security Center vyberte v části **Pokročilá obrana cloudu**možnost **přístup k virtuálním** počítačům za běhu.

   ![Přístup k virtuálnímu počítači za běhu][1]

   **Přístup k virtuálnímu počítači za běhu** poskytuje informace o stavu virtuálních počítačů:

   - **Nakonfigurováno** – virtuální počítače, které byly nakonfigurovány pro podporu přístupu k virtuálnímu počítači za běhu.
   - **Doporučené** – virtuální počítače, které můžou podporovat přístup k virtuálním počítačům za běhu, ale nejsou nakonfigurované na.
   - **Žádné doporučení** – důvody, proč by se virtuální počítač mohl nedoporučuje:

     - Chybějící NSG – řešení za běhu vyžaduje, aby bylo na místě NSG.
     - Klasický přístup k VIRTUÁLNÍm počítačům (VM) – Security Center za běhu aktuálně podporuje jenom virtuální počítače nasazené prostřednictvím Azure Resource Manager.
     - Jiné – virtuální počítač je v této kategorii, pokud je řešení za běhu vypnuto v zásadách zabezpečení předplatného nebo skupiny prostředků nebo že virtuální počítač nemá veřejnou IP adresu a NSG na něj.

2. Vyberte doporučený virtuální počítač a kliknutím na **Povolit JIT na 1 virtuálním počítači** nakonfigurujte zásady za běhu pro tento virtuální počítač:

   Můžete uložit výchozí porty, které Security Center doporučuje, nebo můžete přidat a nakonfigurovat nový port, na kterém chcete povolit řešení za běhu. V tomto kurzu přidáme port tak, že vyberete **Přidat**.

   ![Přidat konfiguraci portu][2]

3. V části **Přidat konfiguraci portu**identifikujete:

   - Port
   - Typ protokolu
   - Povolené zdrojové IP adresy – rozsahy IP adres můžou získat přístup na schválené žádosti.
   - Maximální doba požadavku – maximální doba, po kterou je možné otevřít konkrétní port

4. Kliknutím na **tlačítko OK** uložte.

## <a name="harden-vms-against-malware"></a>Posílení zabezpečení virtuálních počítačů proti malwaru
Adaptivní řízení aplikací vám pomůže definovat sadu aplikací, které se můžou spouštět v konfigurovaných skupinách prostředků, což mezi další výhody pomáhají posílit vaše virtuální počítače před malwarem. Security Center využívá Machine Learning k analýze procesů spuštěných ve virtuálním počítači a pomůže vám s použitím tohoto inteligentního pravidla použít pravidla přidávání na seznam povolených.

1. Vraťte se do hlavní nabídky Security Center. V části **Pokročilá obrana cloudu**vyberte **Adaptivní řízení aplikací**.

   ![Adaptivní řízení aplikací][3]

   Část **skupiny prostředků** obsahuje tři karty:

   - **Nakonfigurováno**: seznam skupin prostředků obsahujících virtuální počítače, které byly nakonfigurovány pomocí řízení aplikací.
   - **Doporučené**: seznam skupin prostředků, pro které se doporučuje řízení aplikací.
   - **Žádné doporučení**: seznam skupin prostředků obsahujících virtuální počítače bez jakýchkoli doporučení pro řízení aplikací. Například virtuální počítače, ve kterých se aplikace vždycky mění, a nedosáhly stabilního stavu.

2. Vyberte kartu **Doporučené** pro seznam skupin prostředků s doporučeními pro řízení aplikací.

   ![Doporučení pro řízení aplikací][4]

3. Výběrem skupiny prostředků otevřete možnost **vytvořit pravidla řízení aplikací** . V části **Vybrat virtuální počítače**zkontrolujte seznam doporučených virtuálních počítačů a zrušte zaškrtnutí u těch, na které nechcete použít řízení aplikací. V seznamu **vybrat procesy pro pravidla**přidávání na seznam povolených zkontrolujte seznam doporučených aplikací a zrušte zaškrtnutí těch, které nechcete použít. Seznam obsahuje:

   - **Název**: úplná cesta k aplikaci.
   - **Procesy**: počet aplikací, které se nacházejí v každé cestě
   - **Běžné**: hodnota Ano značí, že tyto procesy byly spuštěny na většině virtuálních počítačů v této skupině prostředků.
   - **Zneužitelné**: ikona upozornění značí, jestli by útočník mohl aplikace využít k obejití seznamu povolených aplikací. Doporučuje se tyto aplikace před schválením zkontrolovat.

4. Po dokončení výběru vyberte **vytvořit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Další rychlé starty a kurzy v této kolekci se po tomto rychlém startu sestavují. Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, pokračujte v používání úrovně Standard a nechejte Automatické zřizování zapnuté. Pokud pokračovat nechcete, nebo se chcete vrátit na úroveň Free:

1. Vraťte se do hlavní nabídky Security Center a vyberte **zásady zabezpečení**.
2. Vyberte předplatné nebo zásadu, které chcete vrátit do volného místa. Otevře se **zásada zabezpečení** .
3. V části **součásti zásad**vyberte **cenová úroveň**.
4. Výběrem **bezplatné** můžete změnit předplatné z úrovně Standard na úroveň Free.
5. Vyberte **Uložit**.

Pokud chcete zakázat Automatické zřizování:

1. Vraťte se do hlavní nabídky Security Center a vyberte **zásady zabezpečení**.
2. Vyberte předplatné, pro které chcete zakázat Automatické zřizování.
3. V části **zásady zabezpečení – shromažďování dat**vyberte v části **připojování** **vypnuto** Automatické zřizování.
4. Vyberte **Uložit**.

>[!NOTE]
> Když se Automatické zřizování neodstraní, Microsoft Monitoring Agent z virtuálních počítačů Azure, ve kterých se agent zřídil. Zakázání automatického zřizování omezuje monitorování zabezpečení vašich prostředků.
>

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak omezit vystavení hrozbám pomocí:

> [!div class="checklist"]
> * Konfigurace zásad přístupu k VIRTUÁLNÍm počítačům za běhu, aby poskytovala řízený a auditovaný přístup k virtuálním počítačům pouze v případě potřeby
> * Konfigurace zásady adaptivního řízení aplikací pro kontrolu nad tím, které aplikace se můžou na virtuálních počítačích spouštět

Přejděte k dalšímu kurzu, kde se dozvíte, jak reagovat na incidenty zabezpečení.

> [!div class="nextstepaction"]
> [Kurz: reakce na incidenty zabezpečení](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
