---
title: Upgrade na úroveň Standard – Azure Security Center
description: V tomto rychlém startu se dozvíte, jak upgradovat službu Security Center na cenovou úroveň Standard, která poskytuje dodatečné zabezpečení.
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
ms.openlocfilehash: 3f0d624605f617a8e5ab914c49c4c94a40ebdcc6
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435790"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Rychlý start: Připojení předplatného Azure ke službě Security Center úrovně Standard
Azure Security Center zajišťuje jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi hybridními cloudovými úlohami. Zatímco úroveň Free nabízí pouze omezené zabezpečení vašich prostředků Azure, úroveň Standard tyto možnosti rozšiřuje do místního prostředí a jiných cloudů. Security Center úrovně Standard pomáhá vyhledávat a opravovat ohrožení zabezpečení, blokovat škodlivou aktivitu pomocí ovládacích prvků přístupu a aplikací, detekovat hrozby s využitím analýz a inteligentních funkcí a rychle reagovat v případě útoku. Můžete zkusit Security Center Standard zdarma. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

V tomto článku upgradovat na úroveň Standard pro zvýšení zabezpečení a nainstalovat agenta Log Analytics na virtuálních počítačích ke sledování chyb zabezpečení a hrozeb.

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Pro upgrade předplatného na úroveň Standard musíte mít přiřazenou roli Vlastník předplatného, Přispěvatel předplatného nebo Správce zabezpečení.

## <a name="enable-your-azure-subscription"></a>Povolení předplatného Azure

1. Přihlaste se k [portálu Azure](https://azure.microsoft.com/features/azure-portal/).
2. V nabídce **Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**.

   ![Přehled služby Security Center][2]

Stránka **Security Center – Přehled** poskytuje jednotný přehled o stavu hybridních cloudových úloh a umožňuje tak zkoumat a posuzovat zabezpečení úloh a identifikaci a omezení rizika. Security Center automaticky povolí všechna vaše předplatná Azure, která jste předtím vy nebo jiný uživatel předplatného nepřipojili k úrovni Free.

Seznam předplatných můžete zobrazit a filtrovat kliknutím na položku nabídky **Předplatná**. Security Center teď zahájí posuzování zabezpečení těchto předplatných za účelem identifikace ohrožení zabezpečení. Pokud chcete upravit typy posouzení, můžete upravit zásady zabezpečení. Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení.

Během několika minut od prvního spuštění služby Security Center se může zobrazit následující:

- **Doporučení** způsobů, jak zlepšit zabezpečení předplatných Azure. Po kliknutí na dlaždici **Doporučení** se otevře seznam seřazený podle priority.
- Inventář prostředků **Compute a aplikace**, **Sítě**, **Zabezpečení dat** a **Identita a přístup**, které Security Center právě posuzuje, a stav jejich zabezpečení.

Chcete-li plně využít služby Security Center, musíte provést následující kroky, abyste upgradovali na úroveň Standard a nainstalovali agenta Log Analytics.

## <a name="upgrade-to-the-standard-tier"></a>Upgrade na úroveň Standard
Pro účely rychlých startů a kurzů pro službu Security Center musíte provést upgrade na úroveň Standard. K dispozici je bezplatná zkušební verze Standard Security Center. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). 

1. V hlavní nabídce služby Security Center vyberte **Začínáme**.
 
   ![Začínáme][4]

2. V části **Upgrade** zobrazí Security Center seznam předplatných a pracovních prostorů, ke kterým se můžete připojit. 
   - Po kliknutí na **Využít zkušební verzi** se zobrazí seznam všech předplatných a pracovních prostorů, které jsou ve stavu bezplatné zkušební verze.
   -    Předplatná a pracovní prostory, které si nemůžete zdarma vyzkoušet, můžete upgradovat.
   -    V bezplatné zkušební verzi můžete začít používat vybrané pracovní prostory a předplatná.
3. Pokud chcete začít používat zkušební verzi vybraných předplatných, klikněte na **Zahájit zkušební období**.


  ![Výstrahy zabezpečení][9]

## <a name="automate-data-collection"></a>Automatizace shromažďování dat
Security Center shromažďuje data z vašich virtuálních počítačů Azure a počítačů umístěných mimo Azure za účelem monitorování ohrožení zabezpečení a hrozeb. Data se shromažďují pomocí agenta Log Analytics, který čte různé konfigurace související se zabezpečením a protokoly událostí z počítače a zkopíruje data do pracovního prostoru pro analýzu. Ve výchozím nastavení pro vás Security Center vytvoří nový pracovní prostor.

Když je povoleno automatické zřizování, Security Center nainstaluje agenta Log Analytics na všechny podporované virtuální počítače Azure a všechny nové, které jsou vytvořeny. Automatické zřizování se důrazně doporučuje.

Povolení automatického zřizování agenta Analýzy protokolů:

1. V hlavní nabídce Centra zabezpečení vyberte **Nastavení & ceny**.
2. Na řádku předplatného klikněte na předplatné, u kterého chcete změnit nastavení.
3. Na kartě **Shromažďování dat** nastavte **Automatické zřizování** na **Zapnuto**.
4. Vyberte **Uložit**.
---
  ![Povolení automatického zřizování][6]

Díky tomuto novému přehledu o vašich virtuálních počítačích Azure může Security Center poskytovat další doporučení související se stavem aktualizace systému, konfiguracemi zabezpečení operačního systému, ochranou koncových bodů a může také generovat další výstrahy zabezpečení.

  ![Doporučení][8]

## <a name="clean-up-resources"></a>Vyčištění prostředků
Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud budete chtít pokračovat v práci s následnými kurzy a rychlými starty, ponechte v provozu úroveň Standard a nechte zapnuté automatické zřizování. Pokud neplánujete pokračovat nebo se chcete vrátit na úroveň Free:

1. Vraťte se do hlavní nabídky Centra zabezpečení a vyberte **nastavení cenové &**.
2. Klikněte na předplatné, které chcete změnit na bezplatnou úroveň.
3. Vyberte položku **Cenová úroveň** a pak vyberte **Bezplatné**. Standardní úroveň předplatného se změní na bezplatnou.
5. Vyberte **Uložit**.

Pokud chcete vypnout automatické zřizování:

1. Vraťte se do hlavní nabídky Centra zabezpečení a vyberte **nastavení cenové &**.
2. Vyčistit na předplatné, které chcete zakázat automatické zřizování na.
3. Na kartě **Shromažďování dat** nastavte **Automatické zřizování** na **Vypnuto**.
4. Vyberte **Uložit**.

>[!NOTE]
> Zakázání automatické zřizování neodebere agenta Analýzy protokolů z virtuálních stránek Azure, kde byl agent zřízen. Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky.
>

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste upgradovali na úroveň Standard a zjišťovali agenta Log Analytics pro jednotnou správu zabezpečení a ochranu před hrozbami napříč hybridními cloudovými úlohami. Další informace o používání služby Security Center najdete v rychlém startu pro připojení počítačů s Windows v místním prostředí a jiných cloudech.

> [!div class="nextstepaction"]
> [Rychlý start: Připojení počítačů s Windows ke službě Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
