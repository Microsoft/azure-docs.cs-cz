---
title: Kurz pro Azure Security Center – Ochrana prostředků pomocí Azure Security Center | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nakonfigurovat zásadu přístupu k virtuálním počítačům podle potřeby a zásadu řízení aplikací.
services: security-center
documentationcenter: na
author: monhaber
manager: MBaldwin
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: monhaber
ms.openlocfilehash: df9e804e8b8f3a9b40a18873f61ec96edee1503d
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490245"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Kurz: Ochrana prostředků pomocí Azure Security Center
Security Center omezuje vaše vystavení hrozbám díky tomu, že pomocí řízení přístupu a aplikací blokuje škodlivé aktivity. Přístup k virtuálním počítačům (VM) just-in-Time (JIT) omezuje vaše vystavení útokům umožňuje odepření trvalého přístupu k virtuálním počítačům. Místo toho můžete poskytovat řízený a auditovaný přístup k virtuálním počítačům pouze v případě potřeby. Adaptivní řízení aplikací pomáhá posílit ochranu virtuálních počítačů před malwarem díky tomu, že řídí, které aplikace se na virtuálních počítačích můžou spouštět. Služba Security Center pomocí strojového učení analyzuje procesy spuštěné na virtuálním počítači a pomáhá s aplikováním pravidel přidávání na seznam povolených na základě těchto informací.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace zásady přístupu k virtuálním počítačům podle potřeby
> * Konfigurace zásady řízení aplikací

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Chcete-li si vyzkoušet postupy popsané v tomto kurzu, budete potřebovat službu Security Center v cenové úrovni Standard. Security Center úrovně Standard můžete vyzkoušet zdarma. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). Článek Rychlý Start: [Onboarding předplatného Azure na Security Center Standard](security-center-get-started.md) vás provede postupem upgradu na úroveň Standard.

## <a name="manage-vm-access"></a>Správa přístupu k virtuálním počítačům
Chcete-li zamezit příchozímu provozu na virtuální počítače Azure, tím omezit vystavení útokům při poskytování snadného přístupu pro připojení k virtuálním počítačům v případě potřeby je možné přístup k virtuálnímu počítači podle potřeby.

Porty pro správu nemusí být otevřené nepřetržitě. Musí být otevřené pouze během připojení k virtuálnímu počítači, například kvůli provádění úloh správy nebo údržby. Když je povolený přístup podle potřeby, Security Center využívá pravidla skupin zabezpečení sítě (NSG) omezující přístup k portům pro správu, aby na ně nemohli cílit útočníci.

1. V hlavní nabídce služby Security Center, vyberte **Just-in-Time virtuální počítač přístup k** pod **POKROČILÁ OBRANA cloudu**.

  ![Přístup k virtuálním počítačům podle potřeby][1]

  **Přístup k virtuálním počítačům just-in-Time** poskytuje informace o stavu vašich virtuálních počítačů:

  - **Nakonfigurované** – Virtuální počítače s nakonfigurovanou podporou přístupu podle potřeby.
  - **Doporučené** – Virtuální počítače, které můžou podporovat přístup podle potřeby, ale ještě tak nebyly nakonfigurované.
  - **Žádné doporučení** – Mezi důvody, proč virtuální počítač nemusí být doporučený, patří:

    - Chybějící NSG – Řešení přístupu podle potřeby vyžaduje existenci NSG.
    - Klasický virtuální počítač – Přístup k virtuálním počítačům podle potřeby v Security Center aktuálně podporuje pouze virtuální počítače nasazené prostřednictvím Azure Resource Manageru.
    - Jiné – Virtuální počítač je v této kategorii, pokud je řešení přístupu podle potřeby vypnuté v zásadách zabezpečení předplatného nebo skupiny prostředků nebo pokud virtuální počítač nemá veřejnou IP adresu a NSG.

2. Vyberte doporučený virtuální počítač a kliknutím na **Povolit přístup podle potřeby na 1 virtuálním počítači** nakonfigurujte pro tento virtuální počítač zásadu přístupu podle potřeby:

  Můžete uložit výchozí porty, které Security Center doporučuje, nebo přidat a nakonfigurovat nový port, na kterém chcete řešení přístupu podle potřeby povolit. V tomto kurzu přidáme port výběrem možnosti **Přidat**.

  ![Přidání konfigurace portu][2]

3. V části **Přidání konfigurace portu** určíte:

  - Port
  - Typ protokolu
  - Povolené zdrojové IP adresy – Rozsahy IP adres s povolením získat přístup po schválení žádosti
  - Maximální doba žádosti – Maximální časový interval otevření konkrétního portu

4. Výběrem **OK** konfiguraci uložte.

## <a name="harden-vms-against-malware"></a>Posílení ochrany virtuálních počítačů před malwarem
Adaptivní řízení aplikací pomáhá definovat sadu aplikací, které mají povoleno spouštění v nakonfigurovaných skupinách prostředků. To kromě jiných výhod pomáhá posílit ochranu virtuálních počítačů před malwarem. Služba Security Center pomocí strojového učení analyzuje procesy spuštěné na virtuálním počítači a pomáhá s aplikováním pravidel přidávání na seznam povolených na základě těchto informací.

Tato funkce je dostupná pouze pro počítače s Windows.

1. Vraťte se do hlavní nabídky Security Center. V části **POKROČILÁ OBRANA CLOUDU** vyberte **Adaptivní řízení aplikací**.

   ![Adaptivní řízení aplikací][3]

  Část **Skupiny prostředků** obsahuje tři karty:

  - **Nakonfigurované**: Seznam skupin prostředků obsahujících virtuální počítače s nakonfigurovaným řízením aplikací.
  - **Doporučené**: Seznam skupin prostředků, pro kterou aplikaci se doporučuje řízení.
  - **Žádné doporučení**: Seznam skupin prostředků obsahujících virtuální počítače bez jakýchkoli doporučených řízení aplikací. Například virtuální počítače, na kterých se neustále mění aplikace a které se ještě nedostaly do stabilního stavu.

2. Výběrem karty **Doporučené** zobrazte seznam skupin prostředků s doporučeními pro řízení aplikací.

  ![Doporučení pro řízení aplikací][4]

3. Výběrem skupiny prostředků otevřete možnost **Vytvořit pravidla řízení aplikací**. V části **Vybrat virtuální počítače** zkontrolujte seznam doporučených virtuálních počítačů a zrušte zaškrtnutí těch, na které nechcete použít řízení aplikací. V části **Vybrat procesy pro pravidla přidávání na seznam povolených** zkontrolujte seznam doporučených aplikací a zrušte zaškrtnutí těch, které nechcete použít. Seznam obsahuje:

  - **NÁZEV**: Úplná cesta aplikace
  - **PROCESY**: Počet aplikací se nacházejí v jednotlivých cestách
  - **BĚŽNÉ**: "Ano" znamená, že tyto procesy spustily na většině virtuálních počítačů v této skupině prostředků
  - **ZNEUŽITELNÉ**: Ikona upozornění značí, pokud aplikace může používat útočník k obejití seznamu povolených aplikací. Tyto aplikace doporučujeme před schválením zkontrolovat.

4. Jakmile budete s výběry hotovi, vyberte **Vytvořit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud budete chtít pokračovat v práci s následnými kurzy a rychlými starty, ponechte v provozu úroveň Standard a nechte zapnuté automatické zřizování. Pokud neplánujete pokračovat nebo se chcete vrátit na úroveň Free:

1. Vraťte se do hlavní nabídky služby Security Center a vyberte **Zásady zabezpečení**.
2. Vyberte předplatné nebo zásady, které chcete vrátit na úroveň Free. Otevře se okno **Zásady zabezpečení**.
3. V části **SOUČÁSTI ZÁSAD** vyberte **Cenová úroveň**.
4. Výběrem **Free** změníte předplatné z úrovně Standard na úroveň Free.
5. Vyberte **Uložit**.

Pokud chcete vypnout automatické zřizování:

1. Vraťte se do hlavní nabídky služby Security Center a vyberte **Zásady zabezpečení**.
2. Vyberte předplatné, pro které chcete vypnout automatické zřizování.
3. V části **Zásady zabezpečení – shromažďování dat** výběrem možnosti **Vypnuto** u volby **Onboarding** vypnete automatické zřizování.
4. Vyberte **Uložit**.

>[!NOTE]
> Vypnutím automatického zřizování neodeberete agenta Microsoft Monitoring Agent z virtuálních počítačů Azure, na kterých byl agent zřízen. Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky.
>

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak omezit své vystavení hrozbám prostřednictvím:

> [!div class="checklist"]
> * Konfigurace zásady přístupu k virtuálním počítačům podle potřeby, která poskytuje řízený a auditovaný přístup k virtuálním počítačům pouze v případě potřeby.
> * Konfigurace zásady adaptivního řízení aplikací, která řídí, které aplikace se na virtuálních počítačích můžou spouštět.

V dalším kurzu najdete informace o reakci na incidenty zabezpečení.

> [!div class="nextstepaction"]
> [Kurz: Reakce na incidenty zabezpečení](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
