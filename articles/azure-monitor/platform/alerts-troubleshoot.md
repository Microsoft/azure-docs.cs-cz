---
title: Poradce při potížích s výstrahami a oznámeními azure monitoru
description: Běžné problémy s výstrahami Azure Monitor a možná řešení.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132330"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Řešení problémů ve výstrahách Azure Monitoru 

Tento článek popisuje běžné problémy ve službě Azure Monitor výstrahy.

Azure Monitor výstrahy proaktivně vás upozorní, když jsou nalezeny důležité podmínky v datech monitorování. Umožňují identifikovat a řešit problémy dříve, než si jich uživatelé systému všimnou. Další informace o upozorňování najdete [v tématu Přehled výstrah v Microsoft Azure](alerts-overview.md).

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Akce nebo oznámení na mém záznamu nefungovalo podle očekávání

Pokud se vám na webu Azure Portal zobrazí vypálená výstraha, ale máte problém s některými jeho akcemi nebo oznámeními, přečtěte si následující části.

## <a name="did-not-receive-expected-email"></a>Neobdrželi jste očekávaný e-mail

Pokud se vám na webu Azure Portal zobrazuje vypalovaná výstraha, ale neobdrželi jste e-mail, který jste o ní nakonfigurovali, postupujte takto: 

1. **Byl e-mail potlačen [pravidlem akce](alerts-action-rules.md)**? 

    Zaškrtněte kliknutím na vypálenou výstrahu na portálu a podívejte se na kartu Historie pro potlačené [skupiny akcí](action-groups.md): 

    ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)

1. **Je typ akce "E-mail role Správce prostředků Azure"?**

    Tato akce se zaměřuje jenom na přiřazení rolí Správce prostředků Azure, které jsou v oboru předplatného a typu *Uživatel*.  Ujistěte se, že jste přiřadili roli na úrovni předplatného a ne na úrovni prostředků nebo skupiny prostředků.

1. **Přijímá váš e-mailový server a poštovní schránka externí e-maily?**

    Ověřte, zda nejsou blokovány e-maily z těchto tří adres:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Je běžné, že interní e-mailové konference nebo distribuční seznamy blokují e-maily z externích e-mailových adres. Musíte whitelist výše uvedené e-mailové adresy.  
    Chcete-li otestovat, přidejte do skupiny akcí běžnou pracovní e-mailovou adresu (nikoli seznam adresátů) a zjistěte, zda na tento e-mail dorazí upozornění. 

1. **Byl e-mail zpracován pravidly doručené pošty nebo filtrem nevyžádané pošty?** 

    Ověřte, zda neexistují žádná pravidla doručené pošty, která by tyto e-maily odstranila nebo je přesunula do boční složky. Pravidla doručené pošty mohou například zachytit konkrétní odesílatele nebo určitá slova v předmětu.

    Také zkontrolujte:
    
      - nastavení spamu vašeho e-mailového klienta (například Outlook, Gmail)
      - limity odesílatele / nastavení spamu / nastavení karantény vašeho e-mailového serveru (jako Exchange, Office 365, G-suite)
      - nastavení vašeho e-mailového bezpečnostního zařízení, pokud existuje (jako Barracuda, Cisco). 

1. **Neodhlásili jste se omylem z akční skupiny?** 

    E-maily s upozorněním poskytují odkaz na odhlášení ze skupiny akcí. Chcete-li zkontrolovat, zda jste se omylem odhlásili z této skupiny akcí, a to buď:

    1. Otevřete skupinu akcí na portálu a zkontrolujte sloupec Stav:

    ![sloupec stavu skupiny akcí](media/alerts-troubleshoot/action-group-status.png)

    2. Vyhledejte v e-mailu potvrzení o odhlášení:

    ![odhlášení ze skupiny akcí výstrah](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Chcete-li se přihlásit znovu - použijte odkaz v e-mailu s potvrzením odběru, který jste obdrželi, nebo odeberte e-mailovou adresu ze skupiny akcí a pak ji znovu přidejte. 
 
1. **Byli jste hodnoceni jako omezeni kvůli mnoha e-mailům, které jdou na jednu e-mailovou adresu?** 

    E-mail je [omezen](alerts-rate-limiting.md) na ne více než 100 e-mailů každou hodinu na každou e-mailovou adresu. Pokud tuto prahovou hodnotu překročíte, dojde k vynechání dalších e-mailových oznámení.  Zkontrolujte, zda jste obdrželi zprávu, že vaše e-mailová adresa byla dočasně omezena: 
 
   ![E-mailová sazba je omezená](media/alerts-troubleshoot/email-paused.png)

   Pokud chcete dostávat velký objem oznámení bez omezení rychlosti, zvažte použití jiné akce, jako je webhook, aplikace logiky, funkce Azure nebo automatizační sady runbook, z nichž žádná není omezená. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Neobdrželi jste očekávanou SMS, hlasový hovor ani nabízené oznámení

Pokud na portálu vidíte vypalovanou výstrahu, ale neobdrželi jste sms, hlasové volání nebo nabízené oznámení, které jste o něm nakonfigurovali, postupujte takto: 

1. **Byla akce potlačena [pravidlem akce](alerts-action-rules.md)?** 

    Zaškrtněte kliknutím na vypálenou výstrahu na portálu a podívejte se na kartu Historie pro potlačené [skupiny akcí](action-groups.md): 

    ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)

   Pokud to bylo neúmyslné, můžete upravit, zakázat nebo odstranit pravidlo akce.
 
1. **SMS / hlas: Je vaše telefonní číslo správné?**

   Překlepy v kódu země nebo na telefonním čísle najdete v akci SMS. 
 
1. **SMS / hlas: byli jste sazba omezena?** 

    SMS a hlasové hovory jsou omezeny na ne více než jedno oznámení každých pět minut na telefonní číslo. Pokud tuto prahovou hodnotu předáte, oznámení budou zrušena. 

      - Hlasové volání – zkontrolujte historii hovorů a zjistěte, jestli jste měli jiný hovor z Azure v předchozích pěti minutách. 
      - SMS - zkontrolujte historii SMS, zda nezobrazuje zprávu, že vaše telefonní číslo bylo omezeno. 

    Pokud chcete dostávat velký objem oznámení bez omezení rychlosti, zvažte použití jiné akce, jako je webhook, aplikace logiky, funkce Azure nebo automatizační sady runbook, z nichž žádná není omezená. 
 
1. **SMS: Neodhlásili jste se omylem z akční skupiny?**

    Otevřete historii SMS a zkontrolujte, zda jste se odhlásili z doručování SMS z této konkrétní skupiny akcí (pomocí odpovědi ZAKÁZAT action_group_short_name) nebo ze všech skupin akcí (pomocí odpovědi STOP). Chcete-li se přihlásit znovu, odešlete příslušný příkaz SMS (ENABLE action_group_short_name nebo START) nebo odeberte akci SMS ze skupiny akcí a pak ji znovu přidejte zpět.  Další informace naleznete v tématu [CHOVÁNÍ výstrah sms ve skupinách akcí](alerts-sms-behavior.md).

1. **Zablokovali jste omylem oznámení v telefonu?**

   Většina mobilních telefonů umožňuje blokovat hovory nebo SMS z konkrétních telefonních čísel nebo krátkých kódů nebo blokovat nabízená oznámení z konkrétních aplikací (například mobilní aplikace Azure). Chcete-li zkontrolovat, zda jste oznámení v telefonu omylem zablokovali, vyhledejte v dokumentaci specifické pro operační systém a model telefonu nebo vyzkoušejte jiný telefon a telefonní číslo. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Byl očekáván jiný typ akce, která by se spustila, ale 

Pokud se na portálu zobrazí vypalovaná výstraha, ale její nakonfigurovaná akce se neaktivovala, postupujte takto: 

1. **Byla akce potlačena pravidlem akce?** 

    Zaškrtněte kliknutím na vypálenou výstrahu na portálu a podívejte se na kartu Historie pro potlačené [skupiny akcí](action-groups.md): 

    ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)
 
    Pokud to bylo neúmyslné, můžete upravit, zakázat nebo odstranit pravidlo akce. 

1. **Nespustil se webový hák?**

    1. **Byly zdrojové IP adresy zablokovány?**
    
       Whitelist [IP adresy, které](action-groups.md#action-specific-information) webhooku je volána z.

    1. **Funguje koncový bod webhooku správně?**

       Ověřte, zda je nakonfigurovaný koncový bod webhooku správný a koncový bod funguje správně. Zkontrolujte protokoly webhooku nebo instrumentujte jeho kód, abyste mohli prozkoumat (například protokolovat příchozí datovou část). 

    1. **Voláte Slackovi nebo Microsoft Teams?**  
    Každý z těchto koncových bodů očekává konkrétní formát JSON. Podle [těchto pokynů](action-groups-logic-app.md) nakonfigurujte akci aplikace logiky.

    1. **Líbilo se váš webhooku přestal reagovat nebo vrácené chyby?** 

        Náš časový rozsah pro odpověď webhooku je 10 sekund. Volání webhooku bude opakováno až dvakrát, když jsou vráceny následující stavové kódy HTTP: 408, 429, 503, 504 nebo když koncový bod HTTP nereaguje. První opakování se provede po 10 sekundách. Druhý a poslední pokus se stane po 100 sekundách. Pokud druhé opakování selže, koncový bod se nebude volat znovu dalších 30 minut, a to pro všechny skupiny akcí.

## <a name="action-or-notification-happened-more-than-once"></a>Akce nebo oznámení se stalo více než jednou 

Pokud jste obdrželi oznámení o upozornění (například e-mail nebo SMS) více než jednou nebo akce výstrahy (například webhook nebo azure funkce) byla spuštěna vícekrát, postupujte takto: 

1. **Je to opravdu stejný záznam?** 

    V některých případech více podobných výstrah jsou aktivována přibližně ve stejnou dobu. Takže se může zdát, že stejná výstraha vyvolala své akce vícekrát. Pravidlo výstrahy protokolu aktivit může být například nakonfigurováno tak, aby se spustilo při spuštění události i po dokončení (úspěšné mj. nebo neúspěšné), a to tak, že není filtrováno v poli stavu události. 

    Chcete-li zkontrolovat, zda tyto akce nebo oznámení pochází z různých výstrah, zkontrolujte podrobnosti výstrahy, jako je například jeho časové razítko a id výstrahy nebo jeho id korelace. Případně zkontrolujte seznam vypalovaných výstrah na portálu. V takovém případě byste museli upravit logiku pravidla výstrahy nebo jinak nakonfigurovat zdroj výstrah. 

1. **Opakuje se akce ve více skupinách akcí?** 

    Při vyprávění výstrahy je každá z jejích skupin akcí zpracována nezávisle. Pokud se tedy akce (například e-mailová adresa) zobrazí ve více skupinách aktivovaných akcí, bude volána jednou za skupinu akcí. 

    Chcete-li zkontrolovat, které skupiny akcí byly spuštěny, zkontrolujte kartu historie výstrah. Zobrazí se jak skupiny akcí definované v pravidle výstrahy, tak skupiny akcí přidané do výstrahy pravidly akce: 

    ![Akce opakovaná ve více skupinách akcí](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Akce nebo oznámení má neočekávaný obsah

Pokud jste výstrahu obdrželi, ale domníváte se, že některá její pole chybí nebo jsou nesprávná, postupujte takto: 

1. **Vybrali jste správný formát akce?** 

    Každý typ akce (e-mail, webhook atd.) má dva formáty – výchozí, starší formát a [novější společný formát schématu](alerts-common-schema.md). Při vytváření skupiny akcí určíte požadovaný formát pro akci – různé akce ve skupinách akcí mohou mít různé formáty. 

    Například pro akci webhooku: 

    ![možnost schématu akce webhooku](media/alerts-troubleshoot/webhook.png)

    Zkontrolujte, zda je formát zadaný na úrovni akce co očekáváte. Například jste vyvinuli kód, který reaguje na výstrahy (webhook, funkce, aplikace logiky atd.), očekává jeden formát, ale později v akci vy nebo jiná osoba zadaná jiný formát.  

    Zkontrolujte také formát datové části (JSON) pro [výstrahy protokolu aktivit](activity-log-alerts-webhook.md), pro [výstrahy hledání protokolu](alerts-log-webhook.md) (Application Insights a log analytics), pro upozornění na [metriky](alerts-metric-near-real-time.md#payload-schema), pro [společné schéma výstrah](alerts-common-schema-definitions.md)a pro zastaralé klasické [metriky výstrahy](alerts-webhooks.md).

 
1. **Výstrahy protokolu aktivit: Jsou informace dostupné v protokolu aktivit?** 

    [Výstrahy protokolu aktivit](activity-log-alerts.md) jsou výstrahy, které jsou založeny na událostech zapsaných do protokolu aktivit Azure, jako jsou události týkající se vytváření, aktualizace nebo odstranění prostředků Azure, události stavu služby a stavu prostředků nebo zjištění z Azure Advisor a Zásady Azure. Pokud jste obdrželi výstrahu na základě protokolu aktivit, ale některá pole, která potřebujete, chybí nebo jsou nesprávná, nejprve zkontrolujte události v samotném protokolu aktivit. Pokud prostředek Azure nenapsal pole, která hledáte v jeho události protokolu aktivit, tato pole nebudou zahrnuty do odpovídající výstrahy. 

## <a name="action-rule-is-not-working-as-expected"></a>Pravidlo akce nefunguje podle očekávání. 

Pokud se na portálu zobrazí vypálená výstraha, ale související pravidlo akce nefungovalo očekávaným způsobem, postupujte takto: 

1. **Je pravidlo akce povoleno?** 

    Zkontrolujte sloupec stavu pravidla akce a ověřte, zda je povolena související role akce. 

    ![Grafické](media/alerts-troubleshoot/action-rule-status.png) 

    Pokud není povolena, můžete pravidlo akce povolit tak, že ho vyberete a kliknete na Povolit. 

1. **Jedná se o výstrahu služby ve stavu?** 

    Výstrahy stavu služby (monitorová služba = "Stav služby") nejsou ovlivněny pravidly akce. 

1. **Jednalo se pravidlo akce o vaší pohotovosti?** 

    Zkontrolujte, zda pravidlo akce zpracovalo výstrahu kliknutím na vypálenou výstrahu na portálu a podívejte se na kartu historie.

    Zde je příklad pravidla akce, které potlačuje všechny skupiny akcí: 
 
     ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)

    Zde je příklad pravidla akce přidání další skupiny akcí:

    ![Akce opakovaná ve více skupinách akcí](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Odpovídá oboru a filtru pravidla akce vypalované výstraze?** 

    Pokud si myslíte, že pravidlo akce by měl mít aktivována, ale ne, nebo že by neměl mít aktivována, ale to udělal, pečlivě zkontrolujte rozsah pravidla akce a podmínky filtru versus vlastnosti aktivována výstrahy. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Jak najít ID výstrahy vypáleného upozornění

Při otevírání případu týkajícího se konkrétní hojné výstrahy (například – pokud jste neobdrželi jeho e-mailové oznámení), budete muset zadat id výstrahy. 

Chcete-li ji vyhledat, postupujte takto:

1. Na webu Azure Portal přejděte do seznamu vypálených výstrah a najděte konkrétní výstrahu. Pomocí filtrů můžete vyhledat. 

1. Kliknutím na výstrahu otevřete podrobnosti výstrahy. 

1. Posuňte se dolů v polích upozornění na první kartě (karta souhrn), dokud ji nenajdete, a zkopírujte ji. Toto pole také obsahuje pomocné tlačítko "Kopírovat do schránky", které můžete použít.  

    ![najít id výstrahy](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problém s vytvářením, aktualizací nebo odstraněním pravidel akcí na webu Azure Portal

Pokud jste při pokusu o vytvoření, aktualizaci nebo odstranění [pravidla akce](alerts-action-rules.md)obdrželi chybu , postupujte takto: 

1. **Došlo k chybě oprávnění?**  

    Měli byste mít [buď integrovanou roli přispěvatele monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor), nebo konkrétní oprávnění související s pravidly akcí a výstrahami.

1. **Ověřili jste parametry pravidla akce?**  

    Zkontrolujte [dokumentaci pravidla akce](alerts-action-rules.md)nebo příkaz [PowerShell Set-AzActionRule pravidla akce.](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) 


## <a name="next-steps"></a>Další kroky
- Pokud používáte výstrahu protokolu, přečtěte si také [článek Poradce při potížích s výstrahami protokolu](alert-log-troubleshoot.md).
- Vraťte se na [portál Azure](https://portal.azure.com) a zkontrolujte, jestli jste problém vyřešili pomocí výše uvedených pokynů. 
