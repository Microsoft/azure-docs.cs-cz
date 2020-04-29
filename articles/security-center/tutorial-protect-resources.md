---
title: Kurz pro přístup k & Application Controls – Azure Security Center
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
ms.openlocfilehash: 0b28de7af16053093cd0108224188cdd615fce55
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80435507"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Kurz: Ochrana prostředků pomocí Azure Security Center
Security Center omezuje vaše vystavení hrozbám díky tomu, že pomocí řízení přístupu a aplikací blokuje škodlivé aktivity. Přístup k virtuálnímu počítači za běhu (JIT) snižuje vaši expozici útokům tím, že vám umožní odepřít trvalý přístup k virtuálním počítačům. Místo toho můžete poskytovat řízený a auditovaný přístup k virtuálním počítačům pouze v případě potřeby. Adaptivní řízení aplikací pomáhá posílit ochranu virtuálních počítačů před malwarem díky tomu, že řídí, které aplikace se na virtuálních počítačích můžou spouštět. Služba Security Center pomocí strojového učení analyzuje procesy spuštěné na virtuálním počítači a pomáhá s aplikováním pravidel přidávání na seznam povolených na základě těchto informací.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Konfigurace zásad přístupu k VIRTUÁLNÍm počítačům za běhu
> * Konfigurace zásady řízení aplikací

## <a name="prerequisites"></a>Požadavky
Chcete-li si vyzkoušet postupy popsané v tomto kurzu, budete potřebovat službu Security Center v cenové úrovni Standard. Můžete vyzkoušet Security Center Standard bez jakýchkoli nákladů. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). Článek Rychlý Start: [Onboarding předplatného Azure na Security Center Standard](security-center-get-started.md) vás provede postupem upgradu na úroveň Standard.

## <a name="manage-vm-access"></a>Správa přístupu k virtuálním počítačům
Přístup k virtuálnímu počítači JIT se dá použít k uzamknutí příchozího provozu do virtuálních počítačů Azure. tím se sníží riziko útoků na útoky a zároveň se v případě potřeby zajistí snadný přístup k virtuálním počítačům.

Porty pro správu nemusí být otevřené nepřetržitě. Musí být otevřené pouze během připojení k virtuálnímu počítači, například kvůli provádění úloh správy nebo údržby. Pokud je povolený program just-in-time, Security Center používá pravidla skupiny zabezpečení sítě (NSG), která omezují přístup k portům pro správu, aby na ně nedokázali zacílit útočníci.

1. V hlavní nabídce Security Center vyberte v části **Pokročilá obrana cloudu**možnost **přístup k virtuálním** počítačům za běhu.

   ![Přístup k virtuálnímu počítači za běhu][1]

   **Přístup k virtuálnímu počítači za běhu** poskytuje informace o stavu virtuálních počítačů:

   - **Nakonfigurováno** – virtuální počítače, které byly nakonfigurovány pro podporu přístupu k virtuálnímu počítači za běhu.
   - **Doporučené** – virtuální počítače, které můžou podporovat přístup k virtuálním počítačům za běhu, ale nejsou nakonfigurované na.
   - **Žádné doporučení** – Mezi důvody, proč virtuální počítač nemusí být doporučený, patří:

     - Chybějící NSG – řešení za běhu vyžaduje, aby bylo na místě NSG.
     - Klasický přístup k VIRTUÁLNÍm počítačům (VM) – Security Center za běhu aktuálně podporuje jenom virtuální počítače nasazené prostřednictvím Azure Resource Manager.
     - Jiné – virtuální počítač je v této kategorii, pokud je řešení za běhu vypnuto v zásadách zabezpečení předplatného nebo skupiny prostředků nebo že virtuální počítač nemá veřejnou IP adresu a NSG na něj.

2. Vyberte doporučený virtuální počítač a kliknutím na **Povolit JIT na 1 virtuálním počítači** nakonfigurujte zásady za běhu pro tento virtuální počítač:

   Můžete uložit výchozí porty, které Security Center doporučuje, nebo můžete přidat a nakonfigurovat nový port, na kterém chcete povolit řešení za běhu. V tomto kurzu přidáme port výběrem možnosti **Přidat**.

   ![Přidání konfigurace portu][2]

3. V části **Přidání konfigurace portu** určíte:

   - Port
   - Typ protokolu
   - Povolené zdrojové IP adresy – Rozsahy IP adres s povolením získat přístup po schválení žádosti
   - Maximální doba žádosti – Maximální časový interval otevření konkrétního portu

4. Výběrem **OK** konfiguraci uložte.

## <a name="harden-vms-against-malware"></a>Posílení ochrany virtuálních počítačů před malwarem
Adaptivní řízení aplikací pomáhá definovat sadu aplikací, které mají povoleno spouštění v nakonfigurovaných skupinách prostředků. To kromě jiných výhod pomáhá posílit ochranu virtuálních počítačů před malwarem. Služba Security Center pomocí strojového učení analyzuje procesy spuštěné na virtuálním počítači a pomáhá s aplikováním pravidel přidávání na seznam povolených na základě těchto informací.

1. Vraťte se do hlavní nabídky Security Center. V části **POKROČILÁ OBRANA CLOUDU** vyberte **Adaptivní řízení aplikací**.

   ![Adaptivní řízení aplikací][3]

   Část **Skupiny prostředků** obsahuje tři karty:

   - **Nakonfigurované:** Seznam skupin prostředků obsahujících virtuální počítače s nakonfigurovaným řízením aplikací.
   - **Doporučené:** Seznam skupin prostředků, pro které se doporučuje řízení aplikací.
   - **Žádné doporučení:** Seznam skupin prostředků obsahujících virtuální počítače bez jakýchkoli doporučení pro řízení aplikací. Například virtuální počítače, na kterých se neustále mění aplikace a které se ještě nedostaly do stabilního stavu.

2. Výběrem karty **Doporučené** zobrazte seznam skupin prostředků s doporučeními pro řízení aplikací.

   ![Doporučení pro řízení aplikací][4]

3. Výběrem skupiny prostředků otevřete možnost **Vytvořit pravidla řízení aplikací**. V části **Vybrat virtuální počítače** zkontrolujte seznam doporučených virtuálních počítačů a zrušte zaškrtnutí těch, na které nechcete použít řízení aplikací. V části **Vybrat procesy pro pravidla přidávání na seznam povolených** zkontrolujte seznam doporučených aplikací a zrušte zaškrtnutí těch, které nechcete použít. Seznam obsahuje:

   - **NÁZEV:** Úplná cesta k aplikaci.
   - **PROCESY:** Počet aplikací, které se nacházejí v jednotlivých cestách.
   - **Běžné**: hodnota Ano značí, že tyto procesy byly spuštěny na většině virtuálních počítačů v této skupině prostředků.
   - **Zneužitelné**: ikona upozornění značí, jestli by útočník mohl aplikace využít k obejití seznamu povolených aplikací. Tyto aplikace doporučujeme před schválením zkontrolovat.

4. Jakmile budete s výběry hotovi, vyberte **Vytvořit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, pokračujte v používání úrovně Standard a nechejte Automatické zřizování zapnuté. Pokud neplánujete pokračovat nebo se chcete vrátit na úroveň Free:

1. Vraťte se do hlavní nabídky služby Security Center a vyberte **Zásady zabezpečení**.
2. Vyberte předplatné nebo zásady, které chcete vrátit na úroveň Free. Otevře se okno **Zásady zabezpečení**.
3. V části **SOUČÁSTI ZÁSAD** vyberte **Cenová úroveň**.
4. Výběrem **Free** změníte předplatné z úrovně Standard na úroveň Free.
5. Vyberte **Uložit**.

Pokud chcete vypnout automatické zřizování:

1. Vraťte se do hlavní nabídky Security Center a vyberte **zásady zabezpečení**.
2. Vyberte předplatné, pro které chcete vypnout automatické zřizování.
3. V části **Zásady zabezpečení – shromažďování dat** výběrem možnosti **Vypnuto** u volby **Onboarding** vypnete automatické zřizování.
4. Vyberte **Uložit**.

>[!NOTE]
> Při vypnutí automatického zřizování se agent Log Analytics neodebere z virtuálních počítačů Azure, ve kterých se agent zřídil. Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky.
>

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak omezit své vystavení hrozbám prostřednictvím:

> [!div class="checklist"]
> * Konfigurace zásad přístupu k VIRTUÁLNÍm počítačům za běhu, aby poskytovala řízený a auditovaný přístup k virtuálním počítačům pouze v případě potřeby
> * Konfigurace zásady adaptivního řízení aplikací, která řídí, které aplikace se na virtuálních počítačích můžou spouštět.

V dalším kurzu najdete informace o reakci na incidenty zabezpečení.

> [!div class="nextstepaction"]
> [Kurz: Reakce na incidenty zabezpečení](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
