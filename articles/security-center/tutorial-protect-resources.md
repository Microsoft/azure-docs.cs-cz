---
title: Kurz řízení aplikací access & – Azure Security Center
description: Tento kurz ukazuje, jak nakonfigurovat zásady přístupu k virtuálním počítače just-in-time a zásady řízení aplikací.
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
ms.openlocfilehash: 4b40b7c6f755eb2107a09b1b881ea33fa2187f29
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73686330"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Kurz: Ochrana prostředků pomocí Azure Security Center
Security Center omezuje vaše vystavení hrozbám díky tomu, že pomocí řízení přístupu a aplikací blokuje škodlivé aktivity. Přístup k virtuálnímu počítači (VM) just-in-time (VM) snižuje vaši expozici útokům tím, že vám umožňuje odepřít trvalý přístup k virtuálním počítačům. Místo toho můžete poskytovat řízený a auditovaný přístup k virtuálním počítačům pouze v případě potřeby. Adaptivní řízení aplikací pomáhá posílit ochranu virtuálních počítačů před malwarem díky tomu, že řídí, které aplikace se na virtuálních počítačích můžou spouštět. Služba Security Center pomocí strojového učení analyzuje procesy spuštěné na virtuálním počítači a pomáhá s aplikováním pravidel přidávání na seznam povolených na základě těchto informací.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Konfigurace zásad přístupu k virtuálním virtuálním počítače just-in-time
> * Konfigurace zásady řízení aplikací

## <a name="prerequisites"></a>Požadavky
Chcete-li si vyzkoušet postupy popsané v tomto kurzu, budete potřebovat službu Security Center v cenové úrovni Standard. Můžete zkusit Security Center Standard zdarma. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). Článek Rychlý Start: [Onboarding předplatného Azure na Security Center Standard](security-center-get-started.md) vás provede postupem upgradu na úroveň Standard.

## <a name="manage-vm-access"></a>Správa přístupu k virtuálním počítačům
Přístup k virtuálním počítačům JIT se dá použít k uzamčení příchozího provozu na virtuálních počítačích Azure, což snižuje vystavení útokům a zároveň poskytuje snadný přístup k virtuálním počítačům v případě potřeby.

Porty pro správu nemusí být otevřené nepřetržitě. Musí být otevřené pouze během připojení k virtuálnímu počítači, například kvůli provádění úloh správy nebo údržby. Je-li povoleno just-in-time, security center používá síť group (Network Security Group) pravidla, která omezují přístup k portům pro správu, takže nemohou být cílem útočníků.

1. V hlavní nabídce Centra zabezpečení vyberte v části **Advanced CLOUD DEFENSE**možnost Přístup k virtuálním můlovým virtuálním můlovým **centrům just-in-time** .

   ![Přístup k virtuálnímu počítači za běhu][1]

   **Přístup k virtuálním počítačům just-in-time** poskytuje informace o stavu vašich virtuálních počítačů:

   - **Nakonfigurované** – virtuální počítače, které byly nakonfigurované tak, aby podporovaly přístup k virtuálním počítačem just-in-time.
   - **Doporučeno** – virtuální počítače, které podporují přístup k virtuálním virtuálním počítačem za chvíli, ale nebyly nakonfigurované.
   - **Žádné doporučení** – Mezi důvody, proč virtuální počítač nemusí být doporučený, patří:

     - Chybějící nsg - řešení just-in-time vyžaduje nsg být na místě.
     - Klasický virtuální počítač – přístup k virtuálním počítači centra zabezpečení just-in-time, který momentálně podporuje jenom virtuální počítače nasazené prostřednictvím Správce prostředků Azure.
     - Ostatní – virtuální ho virtuálního počítačů je v této kategorii, pokud je řešení just-in-time vypnuté v zásadách zabezpečení předplatného nebo skupiny prostředků nebo že virtuální ho virtuálního počítačů chybí veřejná IP adresa a nemá skupinu zabezpečení zabezpečení na místě.

2. Vyberte doporučený virtuální počítač a kliknutím **na Povolit JIT na 1 virtuálním počítači** nakonfigurujte zásady just-in-time pro tento virtuální počítač:

   Můžete uložit výchozí porty, které doporučuje Security Center, nebo můžete přidat a nakonfigurovat nový port, na kterém chcete povolit řešení just-in-time. V tomto kurzu přidáme port výběrem možnosti **Přidat**.

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
   - **ČASTÉ**: "Ano" označuje, že tyto procesy byly provedeny na většině virtuálních počítačů v této skupině prostředků
   - **ZNEUŽITELNÉ**: Ikona upozornění označuje, zda by útočník mohl aplikace použít k obejití seznamu povolených aplikací. Tyto aplikace doporučujeme před schválením zkontrolovat.

4. Jakmile budete s výběry hotovi, vyberte **Vytvořit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud máte v plánu pokračovat v práci s následnými rychlými starty a kurzy, pokračujte ve spuštění úrovně Standard a udržujte automatické zřizování povolené. Pokud neplánujete pokračovat nebo se chcete vrátit na úroveň Free:

1. Vraťte se do hlavní nabídky služby Security Center a vyberte **Zásady zabezpečení**.
2. Vyberte předplatné nebo zásady, které chcete vrátit na úroveň Free. Otevře se okno **Zásady zabezpečení**.
3. V části **SOUČÁSTI ZÁSAD** vyberte **Cenová úroveň**.
4. Výběrem **Free** změníte předplatné z úrovně Standard na úroveň Free.
5. Vyberte **Uložit**.

Pokud chcete vypnout automatické zřizování:

1. Vraťte se do hlavní nabídky Centra zabezpečení a vyberte **zásady zabezpečení**.
2. Vyberte předplatné, pro které chcete vypnout automatické zřizování.
3. V části **Zásady zabezpečení – shromažďování dat** výběrem možnosti **Vypnuto** u volby **Onboarding** vypnete automatické zřizování.
4. Vyberte **Uložit**.

>[!NOTE]
> Vypnutím automatického zřizování neodeberete agenta Microsoft Monitoring Agent z virtuálních počítačů Azure, na kterých byl agent zřízen. Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky.
>

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak omezit své vystavení hrozbám prostřednictvím:

> [!div class="checklist"]
> * Konfigurace zásad přístupu k virtuálním virtuálním můcích just-in-time tak, aby poskytovalřízený a auditovaný přístup k virtuálním aplikacím jenom v případě potřeby
> * Konfigurace zásady adaptivního řízení aplikací, která řídí, které aplikace se na virtuálních počítačích můžou spouštět.

V dalším kurzu najdete informace o reakci na incidenty zabezpečení.

> [!div class="nextstepaction"]
> [Kurz: Reakce na incidenty zabezpečení](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
