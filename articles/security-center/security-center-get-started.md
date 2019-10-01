---
title: Rychlý Start Azure Security Center – připojení předplatného Azure k Security Center Standard | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak upgradovat na cenovou úroveň Security Center úrovně Standard pro další zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 689949b2b6fea1dcd06741fd3fc19c9371a96784
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676382"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Rychlý Start: připojení předplatného Azure k Security Center Standard
Azure Security Center poskytuje jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi úlohami hybridního cloudu. I když úroveň Free nabízí jenom omezené zabezpečení pro vaše prostředky Azure, úroveň Standard tyto možnosti rozšiřuje na místní a jiné cloudy. Security Center Standard vám pomůže najít a opravit chyby zabezpečení, použít řízení přístupu a aplikací k blokování škodlivých aktivit, zjišťovat hrozby pomocí analýz a inteligentních funkcí a rychle reagovat při útoku. Můžete vyzkoušet Security Center Standard bez jakýchkoli nákladů. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/security-center/).

V tomto článku provedete upgrade na úroveň Standard pro zvýšení zabezpečení a nainstalujete Microsoft Monitoring Agent na virtuální počítače, abyste mohli monitorovat chyby zabezpečení a hrozby.

## <a name="prerequisites"></a>Požadavky
Abyste mohli začít s Security Center, musíte mít předplatné, které byste měli Microsoft Azure. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Pokud chcete upgradovat předplatné na úroveň Standard, musíte mít přiřazenou roli Vlastník předplatného, přispěvatele předplatného nebo správce zabezpečení.

## <a name="enable-your-azure-subscription"></a>Povolení předplatného Azure

1. Přihlaste se k [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. V nabídce **Microsoft Azure** vyberte možnost **Security Center**. **Security Center –** zobrazí se přehled.

   ![Přehled Security Center][2]

**Security Center – přehled** nabízí jednotný pohled na stav zabezpečení vašich hybridních cloudových úloh a umožňuje vám zjišťovat a hodnotit zabezpečení vašich úloh a identifikovat a zmírnit rizika. Security Center automaticky povolí žádné z vašich předplatných Azure, které jste předtím nepřipojili vy nebo jiný uživatel předplatného na úroveň Free.

Seznam předplatných můžete zobrazit a filtrovat kliknutím na položku nabídky **předplatná** . Security Center teď začne vyhodnotit zabezpečení těchto předplatných za účelem identifikace slabých míst zabezpečení. Chcete-li přizpůsobit typy hodnocení, můžete upravit zásady zabezpečení. Zásady zabezpečení definují požadovanou konfiguraci vašich úloh a pomáhají zajistit dodržování požadavků na zabezpečení společnosti nebo legislativních předpisů.

Během několika minut od prvního spuštění Security Center se může zobrazit:

- **Doporučení** , jak zvýšit zabezpečení předplatných Azure. Kliknutím na dlaždici **doporučení** se otevře seznam s určitými prioritami.
- Inventarizace **výpočetních & aplikací**, **sítí**, **zabezpečení dat**a **identit & přístupu k** prostředkům, které jsou nyní vyhodnocovány Security Center společně s stav zabezpečení.

Abyste mohli plně využít výhod Security Center, musíte provést následující kroky, abyste upgradovali na úroveň Standard a nainstalovali Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Upgrade na úroveň Standard
Pro účely Security Center rychlých startů a kurzů musíte upgradovat na úroveň Standard. K dispozici je bezplatná zkušební verze Security Center Standard. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/security-center/). 

1. V hlavní nabídce Security Center vyberte **Začínáme**.
 
   ![Začínáme][4]

2. V části **Upgrade**Security Center seznam předplatných a pracovních prostorů, které mají nárok na registraci. 
   - Můžete kliknout na rozbalitelné **použití zkušební verze** , abyste viděli seznam všech předplatných a pracovních prostorů se stavem nároku na zkušební verzi.
   -    Můžete upgradovat předplatná a pracovní prostory, které nejsou vhodné k vyzkoušení.
   -    Můžete vybrat opravňující pracovní prostory a odběry, abyste mohli zahájit zkušební období.
3. Kliknutím na **zahájit zkušební období** spustíte zkušební verzi na vybraných předplatných.


  ![výstrahy zabezpečení][9]

## <a name="automate-data-collection"></a>Automatizace shromažďování dat
Security Center shromažďuje data z vašich virtuálních počítačů Azure a počítačů mimo Azure za účelem monitorování ohrožení zabezpečení a hrozeb. Data se shromažďují pomocí Microsoft Monitoring Agent, která čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do pracovního prostoru pro účely analýzy. Ve výchozím nastavení Security Center vytvoří pro vás nový pracovní prostor.

Když je povolené Automatické zřizování, Security Center nainstaluje Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených. Automatické zřizování se důrazně doporučuje.

Povolení automatického zřizování Microsoft Monitoring Agent:

1. V hlavní nabídce Security Center vyberte **cenové & nastavení**.
2. Na řádku předplatného klikněte na předplatné, pro které chcete změnit nastavení.
3. Na kartě **shromažďování dat** nastavte **Automatické zřizování** na **zapnuto**.
4. Vyberte **Uložit**.
---
  ![Povolit automatické zřizování][6]

Díky tomuto novému přehledu o virtuálních počítačích Azure může Security Center poskytovat další doporučení týkající se stavu aktualizace systému, konfigurace zabezpečení operačního systému, ochrany koncových bodů a také generování dalších výstrah zabezpečení.

  ![Doporučit][8]

## <a name="clean-up-resources"></a>Vyčištění prostředků
Další rychlé starty a kurzy v této kolekci se po tomto rychlém startu sestavují. Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, pokračujte v používání úrovně Standard a nechejte Automatické zřizování zapnuté. Pokud pokračovat nechcete, nebo se chcete vrátit na úroveň Free:

1. Vraťte se do hlavní nabídky Security Center a vyberte **cenové & nastavení**.
2. Klikněte na předplatné, které chcete změnit na úroveň Free.
3. Vyberte **cenovou úroveň** a vyberte **volné** , pokud chcete změnit předplatné z úrovně Standard na úroveň Free.
5. Vyberte **Uložit**.

Pokud chcete zakázat Automatické zřizování:

1. Vraťte se do hlavní nabídky Security Center a vyberte **cenové & nastavení**.
2. Vyčistěte u předplatného, u kterého chcete zakázat Automatické zřizování.
3. Na kartě **shromažďování dat** nastavte **Automatické zřizování** na **vypnuto**.
4. Vyberte **Uložit**.

>[!NOTE]
> Když se Automatické zřizování neodstraní, Microsoft Monitoring Agent z virtuálních počítačů Azure, ve kterých se agent zřídil. Zakázání automatického zřizování omezuje monitorování zabezpečení vašich prostředků.
>

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste upgradovali na úroveň Standard a zřídili Microsoft Monitoring Agent pro jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi úlohami hybridního cloudu. Další informace o tom, jak používat Security Center, najdete v rychlém startu pro připojování počítačů s Windows v místním prostředí i v jiných cloudech.

> [!div class="nextstepaction"]
> [Rychlý Start: připojení počítačů s Windows k Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
