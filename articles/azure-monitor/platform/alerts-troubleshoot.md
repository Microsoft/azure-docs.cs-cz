---
title: Řešení potíží s Azure Monitor výstrahami a oznámeními
description: Běžné problémy s výstrahami Azure Monitor a možnými řešeními.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132330"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Řešení potíží s výstrahami Azure Monitor 

Tento článek popisuje běžné problémy při Azure Monitor upozorňování.

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy předtím, než si ji uživatelé vašeho systému všimnete. Další informace o upozorňování najdete v tématu [Přehled výstrah v Microsoft Azure](alerts-overview.md).

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Akce nebo oznámení na mém upozornění nefungovalo podle očekávání

Pokud se zobrazí výstraha aktivované v Azure Portal, ale došlo k potížím s některými jeho akcemi nebo oznámeními, přečtěte si následující oddíly.

## <a name="did-not-receive-expected-email"></a>Nedostal se očekávaný e-mail.

Pokud se v Azure Portal zobrazí výstraha aktivované, ale nedostala jste e-mailem, který jste si nakonfigurovali, postupujte takto: 

1. **Byl e-mail potlačen [pravidlem akce](alerts-action-rules.md)**? 

    Zkontrolujte kliknutím na výstrahu aktivované na portálu a podívejte se na kartu Historie pro potlačené [skupiny akcí](action-groups.md): 

    ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)

1. **Je typ akce Azure Resource Manager role e-mailu?**

    Tato akce se zabývá pouze Azure Resource Manager přiřazení rolí, která jsou v oboru předplatného, a typu *uživatel*.  Ujistěte se, že máte přiřazenou roli na úrovni předplatného, a ne na úrovni prostředků nebo skupiny prostředků.

1. **Přijímá váš e-mailový server a poštovní schránku externí e-maily?**

    Ověřte, že e-maily z těchto tří adres nejsou blokované:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Je běžné, že interní seznamy adresátů nebo distribuční seznamy blokují e-maily z externích e-mailových adres. Musíte na výše uvedené e-mailové adresy povolit.  
    Chcete-li otestovat, přidejte do skupiny akcí standardní pracovní e-mailovou adresu (nikoli seznam adresátů) a zjistěte, zda výstrahy přicházejí do tohoto e-mailu. 

1. **Byl e-mail zpracován pomocí pravidel doručené pošty nebo filtru spamu?** 

    Ověřte, že nejsou k dispozici žádná pravidla doručených zpráv, která tyto e-maily odstraní, nebo je přesuňte do složky na straně Pravidla doručené pošty například můžou zachytit konkrétní odesílatele nebo konkrétní slova v předmětu.

    Také ověřte:
    
      - nastavení spamu e-mailového klienta (jako je Outlook, Gmail)
      - nastavení omezení odesílatele/spamu/nastavení karantény e-mailového serveru (jako Exchange, Office 365, G-Suite)
      - nastavení vašeho zařízení zabezpečení e-mailu (například Barracuda, Cisco). 

1. **Omylem jste odhlásili odběr skupiny akcí?** 

    E-maily s výstrahou poskytují odkaz na zrušení odběru skupiny akcí. Pokud chcete zjistit, jestli jste omylem odhlásili odběr této skupiny akcí, postupujte takto:

    1. Otevřete na portálu skupinu akcí a ve sloupci Stav ověřte:

    ![sloupec stavu skupiny akcí](media/alerts-troubleshoot/action-group-status.png)

    2. Vyhledejte e-mail s potvrzením zrušení odběru:

    ![zrušení odběru skupiny akcí výstrah](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Pro opětovné přihlášení – buď použijte odkaz v e-mailu s potvrzením o odhlášení, který jste přijali, nebo odeberte e-mailovou adresu ze skupiny akcí a pak ji znovu přidejte. 
 
1. **Dosáhli jste limitu omezeného z důvodu velkého počtu e-mailů na jednu e-mailovou adresu?** 

    Počet e-mailů je [omezen](alerts-rate-limiting.md) na více než 100 e-mailů každé hodiny na každou e-mailovou adresu. Pokud předáte tuto prahovou hodnotu, budou další e-mailová oznámení vyřazena.  Ověřte, jestli jste obdrželi zprávu s oznámením, že vaše e-mailová adresa je dočasně omezená: 
 
   ![Omezená e-mailová rychlost](media/alerts-troubleshoot/email-paused.png)

   Pokud chcete dostávat velké množství oznámení bez omezení rychlosti, zvažte použití jiné akce, jako je Webhook, aplikace logiky, funkce Azure Functions nebo Runbooky Automation. žádná z nich není omezená. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Nedostalo se očekávané služby SMS, hlasový hovor nebo nabízené oznámení.

Pokud se na portálu zobrazí aktivované upozornění, ale neobdrželo SMS, hlasový hovor nebo nabízené oznámení, které o něm jste nakonfigurovali, postupujte takto: 

1. **Byla akce potlačena [pravidlem akce](alerts-action-rules.md)?** 

    Zkontrolujte kliknutím na výstrahu aktivované na portálu a podívejte se na kartu Historie pro potlačené [skupiny akcí](action-groups.md): 

    ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)

   Pokud to nebylo úmyslné, můžete pravidlo akce upravit, zakázat nebo odstranit.
 
1. **SMS/hlas: správné je telefonní číslo?**

   V případě překlepů v kódu země nebo telefonním čísle ověřte akci SMS. 
 
1. **SMS/hlas: dosáhli jste limitu pro omezený počet?** 

    Počet volání SMS a hlasových hovorů je omezen na více než jedno oznámení každých pět minut na jedno telefonní číslo. Pokud předáte tuto prahovou hodnotu, budou oznámení vyhozena. 

      - Hlasový hovor – Zkontrolujte historii hovorů a podívejte se, jestli jste v předchozích pěti minutách nastavili jiné volání z Azure. 
      - SMS – Projděte si historii zprávy SMS s oznámením, že vaše telefonní číslo je omezené. 

    Pokud chcete dostávat velké množství oznámení bez omezení rychlosti, zvažte použití jiné akce, jako je Webhook, aplikace logiky, funkce Azure Functions nebo Runbooky Automation. žádná z nich není omezená. 
 
1. **SMS: omylem jste odhlásili odběr skupiny akcí?**

    Otevřete historii serveru SMS a ověřte, zda jste si z této konkrétní skupiny akcí vykázali doručování služby SMS (pomocí odpovědi action_group_short_name zakázat), nebo ze všech skupin akcí (pomocí odpovědi na STOPu). Chcete-li se přihlásit k odběru znovu, buď odešlete příslušný příkaz serveru SMS (povolte action_group_short_name nebo spustit), nebo odeberte akci serveru SMS ze skupiny akcí a pak ji znovu přidejte.  Další informace najdete v tématu [chování výstrah SMS ve skupinách akcí](alerts-sms-behavior.md).

1. **Nechtěně jste zablokovali oznámení na telefonu?**

   Většina mobilních telefonů vám umožní zablokovat volání nebo SMS ze specifických telefonních čísel nebo krátkých kódů nebo blokovat nabízená oznámení ze specifických aplikací (například Mobilní aplikace Azure). Pokud chcete zkontrolovat, jestli jste oznámení na telefonu omylem zablokovali, vyhledejte dokumentaci specifickou pro váš operační systém a model, nebo otestujte pomocí jiného telefonu a telefonního čísla. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Byl očekáván jiný typ akce, která má být aktivována, ale nebyla 

Pokud na portálu vidíte aktivované upozornění, ale jeho konfigurovaná akce nebyla aktivována, postupujte podle těchto kroků: 

1. **Byla akce potlačena pravidlem akce?** 

    Zkontrolujte kliknutím na výstrahu aktivované na portálu a podívejte se na kartu Historie pro potlačené [skupiny akcí](action-groups.md): 

    ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)
 
    Pokud to nebylo úmyslné, můžete pravidlo akce upravit, zakázat nebo odstranit. 

1. **Neaktivoval se Webhook?**

    1. **Byly zdrojové IP adresy blokované?**
    
       Seznam povolených [IP adres](action-groups.md#action-specific-information) , ze kterých se volá Webhook

    1. **Funguje koncový bod Webhooku správně?**

       Ověřte, že je koncový bod Webhooku, který jste nakonfigurovali, správný a koncový bod funguje správně. Zkontrolujte protokoly Webhooku nebo Instrumentujte svůj kód, abyste mohli prozkoumat (například protokolovat příchozí datovou část). 

    1. **Zavoláte si časovou rezervu nebo Microsoft Teams?**  
    Každý z těchto koncových bodů očekává konkrétní formát JSON. Postupujte podle [těchto pokynů](action-groups-logic-app.md) a místo toho nakonfigurujte akci aplikace logiky.

    1. **Přestal Webhook reagovat nebo vrátil chyby?** 

        Doba vypršení platnosti pro odpověď Webhooku je 10 sekund. Volání Webhooku se bude opakovat až do dvou dodatečných časů, kdy se vrátí následující stavové kódy HTTP: 408, 429, 503, 504 nebo když koncový bod HTTP nereaguje. První opakování se provede po 10 sekundách. Druhý a konečný pokus proběhne po 100 sekundách. Pokud druhé opakování selže, koncový bod se nebude volat znovu dalších 30 minut, a to pro všechny skupiny akcí.

## <a name="action-or-notification-happened-more-than-once"></a>Akce nebo oznámení se stala více než jednou. 

Pokud jste obdrželi oznámení o výstraze (třeba k e-mailu nebo SMS) více než jednou, nebo se akce výstrahy (například Webhooku nebo Azure Function) aktivovala několikrát, postupujte podle těchto kroků: 

1. **Je to opravdu stejná výstraha?** 

    V některých případech se současně spustí více podobných výstrah. To znamená, že se může zdát, že stejná výstraha několikrát aktivovala své akce. Například pravidlo upozornění protokolu aktivit může být nakonfigurované tak, aby se aktivovalo při spuštění události, a když je dokončená (úspěšná nebo neúspěšná), a to tak, že se nefiltruje v poli stav události. 

    Pokud chcete zjistit, jestli tyto akce nebo oznámení pocházejí z různých výstrah, Projděte si podrobnosti výstrahy, jako je jeho časové razítko a ID výstrahy nebo ID korelace. Případně můžete na portálu zaškrtnout seznam aktivované výstrahy. V takovém případě byste potřebovali upravit logiku pravidla výstrahy nebo jinak nakonfigurovat zdroj výstrahy. 

1. **Opakuje se akce ve více skupinách akcí?** 

    Při vyvolání výstrahy se každá z jejích skupin akcí zpracuje nezávisle. Takže pokud se v několika aktivovaných skupinách akcí zobrazí akce (například e-mailová adresa), bude se volat jednou pro skupinu akcí. 

    Chcete-li zjistit, které skupiny akcí byly aktivovány, podívejte se na kartu Historie výstrah. V pravidle výstrahy se zobrazí obě skupiny akcí a skupiny akcí přidané do výstrah podle pravidel akcí: 

    ![Akce se opakuje ve více skupinách akcí.](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Akce nebo oznámení má neočekávaný obsah.

Pokud jste obdrželi výstrahu, ale domníváte se, že některá pole chybí nebo jsou nesprávná, postupujte podle následujících kroků: 

1. **Vybrali jste pro akci správný formát?** 

    Každý typ akce (e-mail, Webhook atd.) má dva formáty – výchozí formát, starší verze a [novější formát Common Schema](alerts-common-schema.md). Když vytvoříte skupinu akcí, určíte požadovaný formát pro akci – různé akce ve skupinách akcí mohou mít různé formáty. 

    Například pro akci Webhooku: 

    ![možnost schématu akce Webhooku](media/alerts-troubleshoot/webhook.png)

    Ověřte, zda je formát zadaný na úrovni akce očekávaný. Mohli jste například vytvořit kód, který reaguje na výstrahy (Webhook, funkce, aplikace logiky atd.), očekával se jeden formát, ale později v akci, kterou jste vy nebo jiný uživatel uvedli jiný formát.  

    Také pro výstrahy metriky [(Application Insights](alerts-log-webhook.md) i Log Analytics) pro upozornění na [metriky](alerts-metric-near-real-time.md#payload-schema)pro [běžné schéma výstrah](alerts-common-schema-definitions.md)a pro nepoužívané [klasické výstrahy metriky](alerts-webhooks.md)si prohlédněte formát datové části (JSON) pro výstrahy [protokolu aktivit](activity-log-alerts-webhook.md).

 
1. **Výstrahy protokolu aktivit: jsou k dispozici informace v protokolu aktivit?** 

    [Výstrahy protokolu aktivit](activity-log-alerts.md) jsou výstrahy založené na událostech zapsaných do protokolu aktivit Azure, jako jsou například události týkající se vytváření, aktualizace nebo odstraňování prostředků Azure, stavu služeb a událostí stavu prostředků nebo zjištění z Azure Advisor a Azure Policy. Pokud jste obdrželi výstrahu na základě protokolu aktivit, ale některá pole, která potřebujete, chybí nebo jsou nesprávná, nejprve ověřte události v samotném protokolu aktivit. Pokud prostředek Azure nezapsal pole, která hledáte v události protokolu aktivit, tato pole nebudou zahrnutá do příslušné výstrahy. 

## <a name="action-rule-is-not-working-as-expected"></a>Pravidlo akce nefunguje podle očekávání 

Pokud na portálu vidíte aktivované upozornění, ale související pravidlo akce nefungovalo podle očekávání, postupujte podle těchto kroků: 

1. **Je pravidlo akce povolené?** 

    Zkontrolujte sloupec stav pravidla akce a ověřte, zda je povolena role související akce. 

    ![objekty](media/alerts-troubleshoot/action-rule-status.png) 

    Pokud není povolená, můžete pravidlo akce povolit tak, že ho vyberete a kliknete na Povolit. 

1. **Je to upozornění na stav služby?** 

    Pravidla akcí neovlivňují upozornění na stav služby (monitor Service = "Service Health"). 

1. **Jednalo pravidlo akce u vaší výstrahy?** 

    Zkontrolujte, jestli pravidlo akce zpracovalo upozornění kliknutím na výstrahu aktivované na portálu a podívejte se na kartu Historie.

    Tady je příklad pravidla akce, které potlačuje všechny skupiny akcí: 
 
     ![Historie potlačení pravidla akce výstrahy](media/alerts-troubleshoot/history-action-rule.png)

    Tady je příklad pravidla akce, které přidává další skupinu akcí:

    ![Akce se opakuje ve více skupinách akcí.](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Odpovídá rozsah pravidla a filtr akcí aktivovanému upozornění?** 

    Pokud se domníváte, že pravidlo akce by mělo být aktivováno, ale nedošlo k jeho vyvolání, pečlivě zkontrolujte rozsah pravidla akcí a podmínky filtru oproti vlastnostem aktivované výstrahy. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Jak najít ID výstrahy aktivované výstrahy

Pokud otevíráte případ týkající se konkrétní aktivované výstrahy (například – pokud jste neobdrželi své e-mailové oznámení), budete muset zadat ID výstrahy. 

Pro vyhledání použijte následující postup:

1. V Azure Portal přejděte do seznamu aktivované výstrahy a najděte konkrétní výstrahu. Můžete použít filtry, které vám ho pomůžou najít. 

1. Kliknutím na výstrahu otevřete podrobnosti výstrahy. 

1. Posuňte se dolů v polích výstrahy první karty (karta souhrn), dokud ji nenajdete, a zkopírujte ji. Toto pole také obsahuje pomocné tlačítko Kopírovat do schránky, které můžete použít.  

    ![najít ID výstrahy](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problémy při vytváření, aktualizaci nebo odstraňování pravidel akcí v Azure Portal

Pokud při pokusu o vytvoření, aktualizaci nebo odstranění [pravidla akce](alerts-action-rules.md)došlo k chybě, postupujte podle následujících kroků: 

1. **Zobrazila se vám Chyba oprávnění?**  

    Měli byste buď mít [integrovanou roli Přispěvatel monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor), nebo specifická oprávnění související s pravidly a výstrahami akcí.

1. **Ověřili jste parametry pravidla akcí?**  

    Podívejte se na [dokumentaci pravidla akce](alerts-action-rules.md)nebo příkaz [PowerShell set-AzActionRule pro pravidlo akce](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) . 


## <a name="next-steps"></a>Další kroky
- Pokud používáte upozornění protokolu, podívejte se také na téma [řešení potíží s výstrahami protokolu](alert-log-troubleshoot.md).
- Vraťte se na [Azure Portal](https://portal.azure.com) a ověřte, jestli jste problém vyřešili výše uvedeným pokynů. 
