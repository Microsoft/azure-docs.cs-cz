---
title: Řešení potíží s Azure Monitor výstrahami a oznámeními
description: Běžné problémy s výstrahami Azure Monitor a možnými řešeními.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: d3f8da7e985e62ce0b40c6dddcd137cce8561e59
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717855"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Řešení potíží s výstrahami Azure Monitor

Tento článek popisuje běžné problémy při Azure Monitor upozorňování a oznámeních.

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy předtím, než si ji uživatelé vašeho systému všimnete. Další informace o upozorňování najdete v tématu [Přehled výstrah v Microsoft Azure](./alerts-overview.md).

Pokud máte problém s vypálením nebo vypálením výstrahy v případě, že jste očekávali, podívejte se na články níže. Výstrahy "aktivované" můžete zobrazit v Azure Portal.

- [Řešení potíží s Azure Monitor výstrahami metrik v Microsoft Azure](alerts-troubleshoot-metric.md)  
- [Řešení potíží s Azure Monitor výstrahami protokolu v Microsoft Azure](alerts-troubleshoot-metric.md)

Pokud se výstraha aktivuje podle Azure Portal, ale neproběhne správná oznámení, použijte informace ve zbývající části tohoto článku k vyřešení tohoto problému.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Akce nebo oznámení na mém upozornění nefungovalo podle očekávání

Pokud se zobrazí výstraha aktivované v Azure Portal, ale došlo k potížím s některými jeho akcemi nebo oznámeními, přečtěte si následující oddíly.

## <a name="did-not-receive-expected-email"></a>Nedostal se očekávaný e-mail.

Pokud se na webu Azure Portal zobrazuje aktivované upozornění, ale neobdrželi jste e-mail, který jste pro upozornění nakonfigurovali, postupujte následovně:

1. **Byl e-mail potlačen [pravidlem akce](../alerts/alerts-action-rules.md)**?

    Kontrolu provedete tak, že na portálu kliknete na aktivované upozornění a na kartě Historie vyhledáte potlačené [skupiny akcí](./action-groups.md):

    ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)

1. **Je typ akce Azure Resource Manager role e-mailu?**

    Tato akce se zabývá pouze Azure Resource Manager přiřazení rolí, která jsou v oboru předplatného, a typu *uživatel*.  Ujistěte se, že jste roli přiřadili na úrovni předplatného, a ne na úrovni prostředku nebo skupiny prostředků.

1. **Přijímá váš e-mailový server a poštovní schránku externí e-maily?**

    Ověřte, že nedochází k blokování e-mailů z těchto tří adres:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Interní seznamy adresátů nebo distribuční seznamy běžně blokují e-maily z externích e-mailových adres. Je nutné, abyste povolili poštu z výše uvedených e-mailových adres.  
    Pokud chcete provést test, přidejte do skupiny akcí normální pracovní e-mailovou adresu (a ne seznam adresátů) a zjistěte, jestli na tento e-mail přicházejí upozornění.

1. **Byl e-mail zpracován pomocí pravidel doručené pošty nebo filtru spamu?**

    Ověřte, že neexistují žádná pravidla doručené pošty, která tyto e-maily odstraňují nebo přesouvají do vedlejší složky. Pravidla doručené pošty například můžou zachytit konkrétní odesílatele nebo konkrétní slova v předmětu.

    Zkontrolujte také následující:

   - nastavení spamu ve vašem e-mailovém klientovi (jako je Outlook nebo Gmail)
      - nastavení omezení a nastavení nevyžádané pošty/karantény e-mailového serveru (například Exchange, Microsoft 365, G-Suite)
      - nastavení vašeho zařízení zabezpečení e-mailu (například Barracuda, Cisco).

1. **Omylem jste odhlásili odběr skupiny akcí?**

    E-mailová upozornění obsahují odkaz k odhlášení odběru skupiny akcí. Pokud chcete zkontrolovat, jestli jste se omylem neodhlásili z odběru této skupiny akcí, postupujte následovně:

    1. Na portálu otevřete skupinu akcí a zkontrolujte sloupec Stav:

    ![Sloupec Stav ve skupině akcí](media/alerts-troubleshoot/action-group-status.png)

    2. Případně ve svém e-mailu vyhledejte potvrzení o odhlášení odběru:

    ![Odhlášení odběru skupiny akcí upozornění](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Pokud se chcete znovu přihlásit k odběru, použijte odkaz v přijatém e-mailu s potvrzením o odhlášení odběru nebo odeberte e-mailovou adresu ze skupiny akcí a pak ji znovu přidejte. 
 
1. **Dosáhli jste limitu omezeného z důvodu velkého počtu e-mailů na jednu e-mailovou adresu?**

    E-mail je [rychlostně omezený](alerts-rate-limiting.md) na maximálně 100 emailů na jednotlivé e-mailové adresy za hodinu. Pokud tuto prahovou hodnotu překročíte, všechna další e-mailová oznámení se zahodí.  Zkontrolujte, jestli jste neobdrželi zprávu s informací o dočasném rychlostním omezení vaší e-mailové adresy: 
 
   ![Rychlostní omezení e-mailu](media/alerts-troubleshoot/email-paused.png)

   Pokud chcete, aby vám přicházelo velké množství oznámení, aniž by docházelo k rychlostnímu omezení, zvažte použití jiné akce, jako jsou webhook, aplikace logiky, funkce Azure nebo runbooky služby Automation, které nejsou rychlostně omezené. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Nedostalo se očekávané služby SMS, hlasový hovor nebo nabízené oznámení.

Pokud se na portálu zobrazuje aktivované upozornění, ale neobdrželi jste SMS, hlasový hovor nebo nabízené oznámení, které jste pro upozornění nakonfigurovali, postupujte následovně: 

1. **Byla akce potlačena [pravidlem akce](../alerts/alerts-action-rules.md)?**

    Kontrolu provedete tak, že na portálu kliknete na aktivované upozornění a na kartě Historie vyhledáte potlačené [skupiny akcí](./action-groups.md): 

    ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)

   Pokud to nebylo záměrné, můžete příslušné pravidlo akce upravit, zakázat nebo odstranit.
 
1. **SMS/hlas: správné je telefonní číslo?**

   Zkontrolujte, jestli akce SMS neobsahuje překlepy v kódu země nebo telefonním čísle.
 
1. **SMS/hlas: dosáhli jste limitu pro omezený počet?**

    SMS a hlasové hovory jsou pro každé telefonní číslo rychlostně omezené na maximálně jedno oznámení každých pět minut. Pokud tuto prahovou hodnotu překročíte, oznámení se zahodí.

      - Hlasový hovor – v historii volání zkontrolujte, jestli jste během posledních pěti minut neobdrželi z Azure jiný hovor.
      - SMS – v historii SMS zkontrolujte, jestli nemáte zprávu s informací o rychlostním omezení vašeho telefonního čísla.

    Pokud chcete, aby vám přicházelo velké množství oznámení, aniž by docházelo k rychlostnímu omezení, zvažte použití jiné akce, jako jsou webhook, aplikace logiky, funkce Azure nebo runbooky služby Automation, které nejsou rychlostně omezené. 
 
1. **SMS: omylem jste odhlásili odběr skupiny akcí?**

    Otevřete historii serveru SMS a ověřte, zda jste si z této konkrétní skupiny akcí vykázali doručování služby SMS (pomocí odpovědi action_group_short_name zakázat), nebo ze všech skupin akcí (pomocí odpovědi na STOPu). Pokud se chcete znovu přihlásit k odběru, odešlete příslušný příkaz SMS (ENABLE krátký_název_skupiny_akcí nebo START) nebo odeberte akci SMS ze skupiny akcí a pak ji znovu přidejte.  Další informace najdete v tématu [Chování upozornění SMS ve skupinách akcí](alerts-sms-behavior.md).

1. **Nechtěně jste zablokovali oznámení na telefonu?**

   Většina mobilních telefonů umožňuje zablokovat volání nebo SMS z konkrétních telefonních čísel nebo krátkých kódů nebo zablokovat nabízená oznámení z konkrétních aplikací (jako je mobilní aplikace Azure). Pokud chcete zkontrolovat, jestli jste na svém telefonu omylem nezablokovali oznámení, prohledejte dokumentaci k operačnímu systému a modelu vašeho telefonu nebo proveďte test s jiným telefonem a telefonním číslem.

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Byl očekáván jiný typ akce, která má být aktivována, ale nebyla 
   
Pokud se na portálu zobrazuje aktivované upozornění, ale jeho nakonfigurovaná akce se neaktivovala, postupujte následovně:

1. **Byla akce potlačena pravidlem akce?**

    Kontrolu provedete tak, že na portálu kliknete na aktivované upozornění a na kartě Historie vyhledáte potlačené [skupiny akcí](./action-groups.md):

    ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)
 
    Pokud to nebylo záměrné, můžete příslušné pravidlo akce upravit, zakázat nebo odstranit.

1. **Neaktivoval se Webhook?**

    1. **Došlo k zablokování zdrojových IP adres?**
    
       Přidejte [IP adresy](./action-groups.md#action-specific-information) , ze kterých se Webhook volá, do seznamu povolených.

    1. **Funguje správně koncový bod webhooku?**

       Ověřte správnost a správné fungování koncového bodu webhooku, který jste nakonfigurovali. Projděte si protokoly webhooku nebo instrumentujte jeho kód, abyste mohli provést šetření (například nastavte protokolování příchozí datové části).

    1. **Voláte Slack nebo Microsoft Teams?**  
    Každý z těchto koncových bodů očekává specifický formát JSON. Postupujte podle [těchto pokynů](../alerts/action-groups-logic-app.md) a nakonfigurujte místo toho akci aplikace logiky.

    1. **Přestal Webhook reagovat nebo vrátil chyby?** 

        Náš časový limit pro odpověď webhooku je 10 sekund. Volání webhooku se bude znovu opakovat až dvakrát v případě, že se vrátí následující stavové kódy HTTP: 408, 429, 503, 504 nebo když koncový bod HTTP neodpovídá. První opakování se provede po 10 sekundách. Druhé a poslední opakování se provede po 100 sekundách. Pokud druhé opakování selže, koncový bod se nebude volat znovu dalších 30 minut, a to pro všechny skupiny akcí.

## <a name="action-or-notification-happened-more-than-once"></a>Akce nebo oznámení se stala více než jednou. 

Pokud jste obdrželi více než jedno oznámení o upozornění (například e-mail nebo SMS) nebo se vícekrát aktivovala akce upozornění (například webhook nebo funkce Azure), postupujte následovně: 

1. **Je to opravdu stejná výstraha?** 

    V některých případech se může přibližně ve stejnou dobu aktivovat více podobných upozornění. Proto se může zdát, že jedno upozornění aktivovalo své akce vícekrát. Například pravidlo upozornění protokolu aktivit může být nakonfigurované tak, aby se aktivovalo při spuštění události i při jejím dokončení (úspěšném nebo neúspěšném), pokud neprovádí filtrování podle pole stavu události. 

    Pokud chcete zkontrolovat, jestli tyto akce nebo oznámení pocházejí z různých upozornění, prozkoumejte podrobnosti o upozornění, například jeho časové razítko a ID upozornění nebo ID korelace. Případně si projděte seznam aktivovaných upozornění na portálu. V takovém případě byste potřebovali upravit logiku pravidla výstrahy nebo jinak nakonfigurovat zdroj výstrahy. 

1. **Opakuje se akce ve více skupinách akcí?** 

    Když se aktivuje upozornění, jeho jednotlivé skupiny akcí se zpracovávají nezávisle na sobě. Takže pokud se nějaká akce (například e-mailová adresa) vyskytuje ve více aktivovaných skupinách akcí, zavolá se jednou pro každou skupinu akcí. 

    Pokud chcete zjistit, které skupiny akcí se aktivovaly, zkontrolujte kartu Historie upozornění. Zobrazí se skupiny akcí definované v pravidlu upozornění i skupiny akcí přidané do upozornění pravidly akcí: 

    ![Akce se opakuje ve více skupinách akcí.](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Akce nebo oznámení má neočekávaný obsah.

Pokud vám upozornění přišlo, ale myslíte si, že některá z jeho polí chybí nebo jsou nesprávná, postupujte následovně: 

1. **Vybrali jste pro akci správný formát?** 

    Každý typ akce (e-mail, Webhook atd.) má dva formáty – výchozí formát, starší verze a [novější formát Common Schema](../alerts/alerts-common-schema.md). Při vytváření skupiny akcí zadáváte, jaký formát pro jednotlivé akce chcete použít – různé akce ve skupinách akcí můžou mít různé formáty. 

    Například pro akci Webhooku: 

    ![možnost schématu akce Webhooku](media/alerts-troubleshoot/webhook.png)

    Zkontrolujte, jestli je na úrovni akce zadaný očekávaný formát. Mohli jste například vytvořit kód, který reaguje na upozornění (webhook, funkce, aplikace logiky atd.) a očekává určitý formát, ale později jste vy nebo někdo jiný v akci zadali jiný formát.  

    Zkontrolujte také, jestli formát datové části neobsahuje [upozornění protokolu aktivit](../alerts/activity-log-alerts-webhook.md), [upozornění prohledávání protokolu](../alerts/alerts-log-webhook.md) (pro Application Insights i Log Analytics), [upozornění na metriky](alerts-metric-near-real-time.md#payload-schema), [společné schéma upozornění](../alerts/alerts-common-schema-definitions.md) nebo zastaralá [klasická upozornění na metriky](./alerts-webhooks.md).

 
1. **Výstrahy protokolu aktivit: jsou k dispozici informace v protokolu aktivit?** 

    [Výstrahy protokolu aktivit](./activity-log-alerts.md) jsou výstrahy založené na událostech zapsaných do protokolu aktivit Azure, jako jsou například události týkající se vytváření, aktualizace nebo odstraňování prostředků Azure, stavu služeb a událostí stavu prostředků nebo zjištění z Azure Advisor a Azure Policy. Pokud jste obdrželi upozornění založené na protokolu aktivit, ale některá požadovaná pole chybí nebo jsou nesprávná, nejprve zkontrolujte události v samotném protokolu aktivit. Pokud prostředek Azure do své události protokolu aktivit požadovaná pole nezapíše, příslušné upozornění tato pole nebude obsahovat. 

## <a name="action-rule-is-not-working-as-expected"></a>Pravidlo akce nefunguje podle očekávání 

Pokud se na portálu zobrazuje aktivované upozornění, ale související pravidlo akce nefungovalo podle očekávání, postupujte následovně: 

1. **Je pravidlo akce povolené?** 

    Ve sloupci Stav pravidla akce ověřte, že je související pravidlo akce povolené. 

    ![objekty](media/alerts-troubleshoot/action-rule-status.png) 

    Pokud pravidlo akce není povolené, můžete ho povolit tím, že ho vyberete a kliknete na Povolit. 

1. **Je to upozornění na stav služby?** 

    Na upozornění na stav služby (služba monitorování = Service Health) se pravidla akcí nevztahují. 

1. **Reagovalo pravidlo akce na vaše upozornění?** 

    Zkontrolujte, jestli pravidlo akce zpracovalo vaše upozornění. Provedete to tak, že na portálu kliknete na aktivované upozornění a podíváte se na kartu Historie.

    Tady je příklad pravidla akce, které potlačuje všechny skupiny akcí: 
 
     ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)

    Tady je příklad pravidla akce, které přidává další skupinu akcí:

    ![Akce se opakuje ve více skupinách akcí.](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Odpovídají rozsah a filtr pravidla akce aktivovanému upozornění?** 

    Pokud si myslíte, že se pravidlo akce mělo aktivovat, ale neaktivovalo se, nebo že se nemělo aktivovat, ale aktivovalo se, pečlivě prozkoumejte rozsah a podmínky filtru pravidla akce a porovnejte je s vlastnostmi aktivovaného upozornění. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Jak najít ID výstrahy aktivované výstrahy

Pokud otevíráte případ týkající se konkrétní aktivované výstrahy (například – pokud jste neobdrželi své e-mailové oznámení), budete muset zadat ID výstrahy. 

Pro vyhledání použijte následující postup:

1. V Azure Portal přejděte do seznamu aktivované výstrahy a najděte konkrétní výstrahu. Můžete použít filtry, které vám ho pomůžou najít. 

1. Kliknutím na výstrahu otevřete podrobnosti výstrahy. 

1. Posuňte se dolů v polích výstrahy první karty (karta souhrn), dokud ji nenajdete, a zkopírujte ji. Toto pole také obsahuje pomocné tlačítko Kopírovat do schránky, které můžete použít.  

    ![najít ID výstrahy](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problémy při vytváření, aktualizaci nebo odstraňování pravidel akcí v Azure Portal

Pokud při pokusu o vytvoření, aktualizaci nebo odstranění [pravidla akce](../alerts/alerts-action-rules.md)došlo k chybě, postupujte podle následujících kroků: 

1. **Zobrazila se vám Chyba oprávnění?**  

    Měli byste buď mít [integrovanou roli Přispěvatel monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor), nebo specifická oprávnění související s pravidly a výstrahami akcí.

1. **Ověřili jste parametry pravidla akcí?**  

    Podívejte se na [dokumentaci pravidla akce](../alerts/alerts-action-rules.md)nebo příkaz [PowerShell set-AzActionRule pro pravidlo akce](/powershell/module/az.alertsmanagement/set-azactionrule?view=azps-3.5.0) . 


## <a name="next-steps"></a>Další kroky
- Pokud používáte upozornění protokolu, podívejte se také na téma [řešení potíží s výstrahami protokolu](./alerts-troubleshoot-log.md).
- Vraťte se na [Azure Portal](https://portal.azure.com) a ověřte, jestli jste problém vyřešili výše uvedeným pokynů.