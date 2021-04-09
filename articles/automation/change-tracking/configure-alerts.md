---
title: Jak vytvořit výstrahy pro Azure Automation Change Tracking a inventář
description: Tento článek obsahuje informace o tom, jak nakonfigurovat výstrahy Azure pro upozorňování na stav změn zjištěných Change Tracking a inventářem.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 91be2f8641a061d009962cdcd03a8d56048594da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594494"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>Jak vytvořit výstrahy pro Change Tracking a inventář

Výstrahy v Azure proaktivně upozorňují na výsledky úloh Runbooku, problémů se stavem služby nebo jiných scénářů týkajících se vašeho účtu Automation. Azure Automation nezahrnuje předem nakonfigurovaná pravidla výstrah, ale můžete vytvořit vlastní, a to na základě dat, která generují. Tento článek poskytuje pokyny k vytváření pravidel výstrah na základě změn identifikovaných Change Tracking a inventářem.

Pokud nejste obeznámeni s výstrahami Azure Monitor, přečtěte si téma [Přehled výstrah v Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md) před tím, než začnete. Další informace o výstrahách, které používají dotazy protokolu, najdete [v tématu protokolování výstrah v Azure monitor](../../azure-monitor/alerts/alerts-unified-log.md).

## <a name="create-alert"></a>Vytvoření upozornění

Následující příklad ukazuje, že soubor **c:\Windows\System32\drivers\etc\hosts** byl změněn v počítači. Tento soubor je důležitý, protože ho systém Windows používá k překladu názvů hostitelů na IP adresy. Tato operace má přednost před DNS a může způsobit problémy s připojením. Může také vést k přesměrování provozu na škodlivé nebo jinak nebezpečné weby.

![Graf znázorňující změnu souboru hostitelů](./media/configure-alerts/changes.png)

Pomocí tohoto příkladu se podíváme na postup, jak na změnu vytvořit výstrahy.

1. Na stránce **Change Tracking (sledování změn** ) účtu Automation vyberte **Log Analytics**.

2. V hledání v protokolech vyhledejte změny obsahu v souboru **hosts** pomocí dotazu `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Tento dotaz vyhledá změny obsahu souborů s plně kvalifikovanými názvy cest obsahujícími slovo `hosts` . Můžete také požádat o konkrétní soubor tak, že změníte část cesty na jeho plně kvalifikovaný tvar, například pomocí `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Poté, co dotaz vrátí své výsledky, vyberte **nové pravidlo výstrahy** v hledání protokolu a otevřete stránku pro **Vytvoření výstrahy** . Na tuto stránku můžete přejít také pomocí **Azure monitor** v Azure Portal.

4. Znovu ověřte dotaz a upravte logiku výstrah. V takovém případě budete chtít aktivovat výstrahu, pokud je ve všech počítačích v prostředí zjištěna i jedna změna.

    ![Změnit na dotaz pro sledování změn v souboru Hosts](./media/configure-alerts/change-query.png)

5. Po nastavení logiky výstrah přiřaďte skupiny akcí, aby prováděly akce v reakci na aktivaci výstrahy. V tomto případě nastavili jsme e-maily, které se mají odeslat, a vytvoří se lístek ITSM (IT Service Management), který se má vytvořit.

Pomocí následujících kroků nastavte výstrahy, které vám pomůžou zjistit stav nasazení aktualizace. Pokud s výstrahami Azure začínáte, přečtěte si téma [Přehled výstrah Azure](../../azure-monitor/alerts/alerts-overview.md).

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurace skupin akcí pro vaše výstrahy

Jakmile budete mít nakonfigurovaná upozornění, můžete nastavit skupinu akcí, která je skupinou akcí pro použití v rámci více výstrah. Akce můžou zahrnovat e-mailová oznámení, Runbooky, Webhooky a spoustu dalších. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](../../azure-monitor/alerts/action-groups.md).

1. Vyberte výstrahu a potom v části **skupiny akcí** vyberte **vytvořit novou** .

2. Zadejte celé jméno a krátký název skupiny akcí. Při odesílání oznámení pomocí zadané skupiny používá Update Management krátký název.

3. V části **Akce** zadejte název, který určuje akci, například **e-mailové oznámení**.

4. Jako **typ akce** vyberte vhodný typ, například **E-mail/zpráva SMS/nabízení/hlas**.

5. Vyberte ikonu tužky a upravte podrobnosti akce.

6. Vyplňte podokno pro typ akce. Pokud například odešlete e-mail pomocí **e-mailu/zprávy SMS/Push/Voice** , zadejte název akce, zaškrtněte políčko **e-mail** , zadejte platnou e-mailovou adresu a pak vyberte **OK**.

    ![Konfigurace e-mailové skupiny akcí](./media/configure-alerts/configure-email-action-group.png)

7. V podokně Přidat skupinu akcí vyberte **OK**.

8. U e-mailu s výstrahou můžete přizpůsobit předmět e-mailu. V části **vytvořit pravidlo** vyberte **přizpůsobit akce** a pak vyberte **Předmět e-mailu**.

9. Jakmile budete hotovi, vyberte **Vytvořit pravidlo upozornění**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [výstrahách v Azure monitor](../../azure-monitor/alerts/alerts-overview.md).

* Přečtěte si o [dotazech protokolu](../../azure-monitor/logs/log-query-overview.md) pro načtení a analýzu dat z log Analyticsho pracovního prostoru.

* Správa [využití a nákladů pomocí Azure Monitorch protokolů](../../azure-monitor/logs/manage-cost-storage.md) popisuje, jak řídit náklady změnou doby uchovávání dat a jak analyzovat a upozorňovat na využití vašich dat.