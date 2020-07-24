---
title: Upgrade na úroveň Standard – Azure Security Center
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
ms.openlocfilehash: f16df87ca72b66003d8870dcd9d778616ea840d4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038541"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Rychlý start: Připojení předplatného Azure ke službě Security Center úrovně Standard
Azure Security Center zajišťuje jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi hybridními cloudovými úlohami. I když úroveň Free nabízí jenom omezené zabezpečení pro vaše prostředky Azure, úroveň Standard tyto možnosti rozšiřuje na místní a jiné cloudy. Security Center úrovně Standard pomáhá vyhledávat a opravovat ohrožení zabezpečení, blokovat škodlivou aktivitu pomocí ovládacích prvků přístupu a aplikací, detekovat hrozby s využitím analýz a inteligentních funkcí a rychle reagovat v případě útoku. Můžete vyzkoušet Security Center Standard bez jakýchkoli nákladů. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

V tomto článku provedete upgrade na úroveň Standard pro zvýšení zabezpečení a nainstalujete agenta Log Analytics na virtuální počítače, abyste mohli monitorovat ohrožení zabezpečení a hrozby.

## <a name="prerequisites"></a>Předpoklady
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Pokud chcete upgradovat předplatné na úroveň Standard, musíte mít přiřazenou roli Vlastník předplatného, přispěvatele předplatného nebo správce zabezpečení.

## <a name="enable-your-azure-subscription"></a>Povolení předplatného Azure

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. V nabídce **Microsoft Azure** vyberte možnost **Security Center**. **Security Center –** zobrazí se přehled.

   ![Přehled služby Security Center][2]

Stránka **Security Center – Přehled** poskytuje jednotný přehled o stavu hybridních cloudových úloh a umožňuje tak zkoumat a posuzovat zabezpečení úloh a identifikaci a omezení rizika. Security Center automaticky povolí všechna vaše předplatná Azure, která jste předtím vy nebo jiný uživatel předplatného nepřipojili k úrovni Free.

Seznam předplatných můžete zobrazit a filtrovat kliknutím na položku nabídky **Předplatná**. Security Center teď zahájí posuzování zabezpečení těchto předplatných za účelem identifikace ohrožení zabezpečení. Pokud chcete upravit typy posouzení, můžete upravit zásady zabezpečení. Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení.

Během několika minut od prvního spuštění služby Security Center se může zobrazit následující:

- **Doporučení** způsobů, jak zlepšit zabezpečení předplatných Azure. Po kliknutí na dlaždici **Doporučení** se otevře seznam seřazený podle priority.
- Inventář prostředků **Compute a aplikace**, **Sítě**, **Zabezpečení dat** a **Identita a přístup**, které Security Center právě posuzuje, a stav jejich zabezpečení.

Abyste mohli plně využít výhod Security Center, musíte provést následující kroky, abyste upgradovali na úroveň Standard a nainstalovali agenta Log Analytics.

## <a name="upgrade-to-the-standard-pricing-tier"></a>Upgrade na cenovou úroveň Standard
Pro účely Security Center rychlých startů a kurzů musíte upgradovat na úroveň Standard. K dispozici je bezplatná zkušební verze Security Center Standard. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). 

1. V hlavní nabídce služby Security Center vyberte **Začínáme**.
 
   ![Začínáme][4]

2. V části **Upgrade** zobrazí Security Center seznam předplatných a pracovních prostorů, ke kterým se můžete připojit. 
   - Po kliknutí na **Využít zkušební verzi** se zobrazí seznam všech předplatných a pracovních prostorů, které jsou ve stavu bezplatné zkušební verze.
   -    Předplatná a pracovní prostory, které si nemůžete zdarma vyzkoušet, můžete upgradovat.
   -    V bezplatné zkušební verzi můžete začít používat vybrané pracovní prostory a předplatná.
3. Pokud chcete začít používat zkušební verzi vybraných předplatných, klikněte na **Zahájit zkušební období**.


  ![Výstrahy zabezpečení][9]

## <a name="automate-data-collection"></a>Automatizace shromažďování dat
Security Center shromažďuje data z vašich virtuálních počítačů Azure a počítačů umístěných mimo Azure za účelem monitorování ohrožení zabezpečení a hrozeb. Data se shromažďují pomocí Log Analytics agenta, který čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do pracovního prostoru pro účely analýzy. Ve výchozím nastavení pro vás Security Center vytvoří nový pracovní prostor.

Když je povolené Automatické zřizování, Security Center nainstaluje agenta Log Analytics na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených. Automatické zřizování se důrazně doporučuje.

Povolení automatického zřizování agenta Log Analytics:

1. V hlavní nabídce Security Center vyberte **cenové & nastavení**.
2. Na řádku předplatného klikněte na předplatné, pro které chcete změnit nastavení.
3. Na kartě **Shromažďování dat** nastavte **Automatické zřizování** na **Zapnuto**.
4. Vyberte **Uložit**.
---
  ![Povolení automatického zřizování][6]

Díky tomuto novému přehledu o vašich virtuálních počítačích Azure může Security Center poskytovat další doporučení související se stavem aktualizace systému, konfiguracemi zabezpečení operačního systému, ochranou koncových bodů a může také generovat další výstrahy zabezpečení.

  ![Doporučení][8]

## <a name="clean-up-resources"></a>Vyčištění prostředků
Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, pokračujte v používání úrovně Standard a nechejte Automatické zřizování zapnuté. Pokud neplánujete pokračovat nebo se chcete vrátit na úroveň Free:

1. Vraťte se do hlavní nabídky Security Center a vyberte **cenové & nastavení**.
2. Klikněte na předplatné, které chcete změnit na úroveň Free.
3. Vyberte **cenovou úroveň** a vyberte **volné** , pokud chcete změnit předplatné z úrovně Standard na úroveň Free.
5. Vyberte **Uložit**.

Pokud chcete vypnout automatické zřizování:

1. Vraťte se do hlavní nabídky Security Center a vyberte **cenové & nastavení**.
2. Vyčistěte u předplatného, u kterého chcete zakázat Automatické zřizování.
3. Na kartě **Shromažďování dat** nastavte **Automatické zřizování** na **Vypnuto**.
4. Vyberte **Uložit**.

>[!NOTE]
> Při vypnutí automatického zřizování se agent Log Analytics neodebere z virtuálních počítačů Azure, ve kterých se agent zřídil. Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky.
>

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste upgradovali na úroveň Standard a zřídili Log Analytics agenta pro sjednocenou správu zabezpečení a ochranu před hrozbami napříč vašimi úlohami hybridního cloudu. Další informace o používání služby Security Center najdete v rychlém startu pro připojení počítačů s Windows v místním prostředí a jiných cloudech.

> [!div class="nextstepaction"]
> [Rychlý start: Připojení počítačů s Windows ke službě Azure Security Center](quick-onboard-windows-computer.md)

Chcete optimalizovat a uložit své útraty do cloudu?

> [!div class="nextstepaction"]
> [Zahájení analýzy nákladů pomocí Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
