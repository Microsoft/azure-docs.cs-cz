---
title: "Rychlý start Azure Security Center – Připojení předplatného Azure ke službě Security Center úrovně Standard | Microsoft Docs"
description: "V tomto rychlém startu se dozvíte, jak upgradovat službu Security Center na cenovou úroveň Standard, která poskytuje dodatečné zabezpečení."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: 0004db332ec13e23ed49f2c19e3454a516ca6a40
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2018
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Rychlý start: Připojení předplatného Azure ke službě Security Center úrovně Standard
Azure Security Center zajišťuje jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi hybridními cloudovými úlohami. Zatímco úroveň Free nabízí pouze omezené zabezpečení vašich prostředků Azure, úroveň Standard tyto možnosti rozšiřuje do místního prostředí a jiných cloudů. Security Center úrovně Standard pomáhá vyhledávat a opravovat ohrožení zabezpečení, blokovat škodlivou aktivitu pomocí ovládacích prvků přístupu a aplikací, detekovat hrozby s využitím analýz a inteligentních funkcí a rychle reagovat v případě útoku. Službu Security Center v cenové úrovni Standard si můžete zdarma vyzkoušet na 60 dní.

V tomto článku provedete upgrade na úroveň Standard, která poskytuje dodatečné zabezpečení, a nainstalujete na své virtuální počítače agenta Microsoft Monitoring Agent, který bude monitorovat ohrožení zabezpečení a hrozby.

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Pro upgrade předplatného na úroveň Standard musíte mít přiřazenou roli Vlastník předplatného, Přispěvatel předplatného nebo Správce zabezpečení.

## <a name="enable-your-azure-subscription"></a>Povolení předplatného Azure

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. V nabídce **Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**.

 ![Přehled služby Security Center][2]

Stránka **Security Center – Přehled** poskytuje jednotný přehled o stavu hybridních cloudových úloh a umožňuje tak zkoumat a posuzovat zabezpečení úloh a identifikaci a omezení rizika. Security Center automaticky povolí všechna vaše předplatná Azure, která jste předtím vy nebo jiný uživatel předplatného nepřipojili k úrovni Free.

Seznam předplatných můžete zobrazit a filtrovat kliknutím na položku nabídky **Předplatná**. Security Center teď zahájí posuzování zabezpečení těchto předplatných za účelem identifikace ohrožení zabezpečení. Pokud chcete upravit typy posouzení, můžete upravit zásady zabezpečení. Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení.

Během několika minut od prvního spuštění služby Security Center se může zobrazit následující:

- **Doporučení** způsobů, jak zlepšit zabezpečení předplatných Azure. Po kliknutí na dlaždici **Doporučení** se otevře seznam seřazený podle priority.
- Inventář prostředků **Compute**, **Sítě**, **Úložiště a data** a **Aplikace**, které Security Center právě posuzuje, a stav jejich zabezpečení.

Pokud chcete využívat všech výhod služby Security Center, musíte dokončením níže uvedených kroků provést upgrade na úroveň Standard a nainstalovat agenta Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Upgrade na úroveň Standard
Pro účely rychlých startů a kurzů pro službu Security Center musíte provést upgrade na úroveň Standard. Prvních 60 dnů je zdarma a kdykoli se můžete vrátit na úroveň Free.

1. V hlavní nabídce služby Security Center vyberte **Připojení k rozšířenému zabezpečení**.

2. V části **Připojení k rozšířenému zabezpečení** zobrazí služba Security Center seznam předplatných a pracovních prostorů, které je možné připojit. Vyberte ze seznamu předplatné.

  ![Výběr předplatného][4]

3. Podokno **Zásady zabezpečení** poskytuje informace o skupinách prostředků v rámci předplatného. Otevře se také podokno **Ceny**.
4. Proveďte upgrade z úrovně Free na úroveň Standard tak, že v části **Ceny** vyberete **Standard** a kliknete na **Uložit**.

  ![Výběr úrovně Standard][5]

Teď, když jste provedli upgrade na úroveň Standard, máte přístup k dalším funkcím služby Security Center, včetně **adaptivního řízení aplikací**, **přístupu k virtuálním počítačům podle potřeby**, **výstrah zabezpečení**, **analýzy hrozeb**, **playbooků automatizace** a dalších. Poznámka: Výstrahy zabezpečení se zobrazí pouze v případě, že služba Security Center detekuje škodlivou aktivitu.

  ![Výstrahy zabezpečení][7]

## <a name="automate-data-collection"></a>Automatizace shromažďování dat
Security Center shromažďuje data z vašich virtuálních počítačů Azure a počítačů umístěných mimo Azure za účelem monitorování ohrožení zabezpečení a hrozeb. Data se shromažďují pomocí agenta Microsoft Monitoring Agent, který z počítače načítá různé protokoly událostí a konfigurace související se zabezpečením a kopíruje data k analýze do vašeho pracovního prostoru. Ve výchozím nastavení pro vás Security Center vytvoří nový pracovní prostor.

Pokud je povoleno automatické zřizování, Security Center nainstaluje agenta Microsoft Monitoring Agent na všechny podporované i nově vytvořené virtuální počítače Azure. Automatické zřizování se důrazně doporučuje.

Povolení automatického zřizování agenta Microsoft Monitoring Agent:

1. V hlavní nabídce služby Security Center vyberte **Zásady zabezpečení**.
2. Vyberte předplatné.
3. V části **Zásady zabezpečení** vyberte **Shromažďování dat**.
4. V části **Shromažďování dat** vyberte **Zapnuto** a povolte tak automatické zřizování.
5. Vyberte **Uložit**.

  ![Povolení automatického zřizování][6]

Díky tomuto novému přehledu o vašich virtuálních počítačích Azure může Security Center poskytovat další doporučení související se stavem aktualizace systému, konfiguracemi zabezpečení operačního systému, ochranou koncových bodů a může také generovat další výstrahy zabezpečení.

  ![Doporučení][8]

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

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste upgradem na úroveň Standard a zřízením agenta Microsoft Monitoring Agent získali jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi hybridními cloudovými úlohami. Další informace o používání služby Security Center najdete v rychlém startu pro připojení počítačů s Windows v místním prostředí a jiných cloudech.

> [!div class="nextstepaction"]
> [Rychlý start: Připojení počítačů s Windows ke službě Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/onboarding.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
