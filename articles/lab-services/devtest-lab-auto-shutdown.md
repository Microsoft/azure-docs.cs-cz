---
title: Správa zásad automatického vypínání v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak nastavit zásady automatického vypnutí pro testovací prostředí tak, aby se virtuální počítače automaticky vypnuly, když se nepoužívají.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: spelluru
ms.openlocfilehash: a2d0b9bdfba1b96ad42e45d54faf106b2361e29d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264779"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Konfigurace automatického vypínání pro testovací a výpočetní virtuální počítače v azure devtest labs

Tento článek vysvětluje, jak nakonfigurovat nastavení automatického vypnutí pro virtuální počítače testovacího prostředí v devTest Labs a výpočetní virtuální počítače. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Konfigurace automatického vypínání pro testovací virtuální počítače (DevTest Labs)
Azure DevTest Labs umožňuje řídit náklady a minimalizovat plýtvání ve vašich testovacích prostředích pomocí správy zásad (nastavení) pro každé testovací prostředí. Tento článek ukazuje, jak nakonfigurovat zásady automatického vypnutí pro účet testovacího prostředí a konfigurovat nastavení automatického vypnutí pro testovací prostředí v účtu testovacího prostředí. Pokud se chcete podívat, jak nastavit všechny zásady testovacího prostředí, [přečtěte si informace o definování zásad testovacího prostředí v laboratořích Azure DevTest .](devtest-lab-set-lab-policy.md)  

### <a name="set-auto-shut-down-policy-for-a-lab"></a>Nastavení zásad automatického vypnutí testovacího prostředí
Jako vlastník testovacího prostředí můžete nakonfigurovat plán vypnutí pro všechny virtuální počítače ve vašem testovacím prostředí. Tímto způsobem můžete ušetřit náklady ze spuštěných počítačů, které nejsou používány (nečinnosti). Můžete vynutit zásady vypnutí na všech virtuálních počítačích testovacího prostředí centrálně, ale také ušetřit uživatelům testovacího prostředí úsilí od nastavení plánu pro jejich jednotlivé počítače. Tato funkce umožňuje nastavit zásady v plánu testovacího prostředí od nabízení žádného ovládacího prvku až po úplné řízení pro uživatele testovacího prostředí. Jako vlastník testovacího prostředí můžete tuto zásadu nakonfigurovat následujícím postupem:

1. Na domovské stránce testovacího prostředí vyberte **Konfigurace a zásady**.
2. V levé nabídce vyberte **zásady** **automatického vypnutí.**
3. Vyberte jednu z možností. V následujících částech jsou uvedeny další podrobnosti o těchto možnostech: Nastavená zásada se vztahuje jenom na nové virtuální aplikace vytvořené v testovacím prostředí a ne na již existující virtuální chody. 

    ![Možnosti automatických vypnutí zásad](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurace nastavení automatického vypnutí
Zásady automatického vypnutí pomáhá minimalizovat plýtvání v laboratoři tím, že umožňuje určit čas, který virtuální počítače tohoto testovacího prostředí vypnout.

Chcete-li zobrazit (a změnit) zásady pro testovací prostředí, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.   
4. Vyberte **možnost Konfigurace a zásady**.

    ![Podokno Nastavení zásad](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **možnost Automatické vypnutí** v části **Plány**.
   
    ![Automatické vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Výběrem **možnosti Zapnuto** tuto zásadu povolíte a **chcete** ji zakázat.
7. Pokud povolíte tuto zásadu, zadejte čas (a časové pásmo) pro vypnutí všech virtuálních zařízení v aktuálním testovacím prostředí.
8. Zadejte **ano** nebo **ne** pro možnost odeslat oznámení 30 minut před zadaným časem automatického vypnutí. Pokud zvolíte **Ano**, zadejte koncový bod adresy URL webhooku nebo e-mailovou adresu určující, kam má být oznámení odesláno nebo odesláno. Uživatel obdrží oznámení a je mu dána možnost odložit vypnutí. Další informace naleznete v části [Oznámení.](#notifications) 
9. Vyberte **Uložit**.

    Ve výchozím nastavení, jakmile je tato zásada povolena, platí tato zásada pro všechny virtuální aplikace v aktuálním testovacím prostředí. Chcete-li toto nastavení odebrat z určitého virtuálního počítače, otevřete podokno správy virtuálního počítače a změňte jeho nastavení **automatického vypnutí.**
    
> [!NOTE]
> Pokud aktualizujete plán automatického vypnutí pro testovací prostředí nebo konkrétní virtuální počítač testovacího prostředí do 30 minut od aktuálního plánovaného času, bude aktualizovaný čas vypnutí platit pro plán na další den. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Uživatel nastaví plán a může se odhlásit
Pokud nastavíte testovací prostředí na tuto zásadu, uživatelé testovacího prostředí můžete přepsat nebo odhlásit z plánu testovacího prostředí. Tato možnost uděluje uživatelům testovacího prostředí plnou kontrolu nad plánem automatického vypnutí jejich virtuálních počítačů. Uživatelům testovacího prostředí se na stránce plánu automatického vypnutí virtuálního počítače nezobrazují žádné změny.

![Možnost automatického vypnutí zásad - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Uživatel nastaví plán a nemůže se odhlásit
Pokud nastavíte testovací prostředí na tuto zásadu, uživatelé testovacího prostředí můžete přepsat plán testovacího prostředí. Nemohou se však odhlásit ze zásad automatického vypnutí. Tato možnost zajišťuje, že každý počítač ve vaší laboratoři je v rámci plánu automatického vypnutí. Uživatelé testovacího prostředí můžou aktualizovat plán automatického vypínání svých virtuálních počítačů a nastavit oznámení o vypnutí.

![Možnost automatického vypnutí zásad - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Uživatel nemá žádnou kontrolu nad plánem nastaveným správcem testovacího prostředí.
Pokud nastavíte testovací prostředí na tuto zásadu, uživatelé testovacího prostředí nelze přepsat nebo odhlásit z plánu testovacího prostředí. Tato možnost nabízí správce testovacího prostředí kompletní ovládací prvek podle plánu pro každý počítač v testovacím prostředí. Uživatelé testovacího prostředí můžou nastavit jenom oznámení o automatickém vypnutí pro své virtuální počítače.

![Možnost automatického vypnutí zásad - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Oznámení
Po automatickém vypnutí nastaveném vlastníkem testovacího prostředí budou oznámení odeslána uživatelům testovacího prostředí 30 minut před spuštěním automatického vypnutí, pokud bude ovlivněn některý z jejich virtuálních počítačů. Tato možnost dává uživatelům testovacího prostředí možnost uložit svou práci před vypnutím. Oznámení také obsahuje odkazy pro každý virtuální virtuální mkér pro následující akce:

- Přeskočte automatické vypnutí pro tuto dobu
- Odložení automatického vypnutí na hodinu nebo 2 hodiny, aby mohli pokračovat v práci na virtuálním počítači.

Oznámení je odesláno prostřednictvím nakonfigurovaného koncového bodu webového zavěšení nebo e-mailové adresy určené vlastníky testovacího prostředí v nastavení automatického vypnutí. Webhooky umožňují vytvářet nebo nastavovat integrace, které se přihlásí k odběru určitých událostí. Při spuštění jedné z těchto událostí DevTest Labs odešle datovou část HTTP POST na nakonfigurovanou adresu URL webhooku. Další informace o webhooky najdete [v tématu vytvoření webhooku nebo funkce Azure rozhraní API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Doporučujeme používat webové háčky, protože jsou značně podporovány různými aplikacemi (například Slack, Azure Logic Apps a tak dále.) a umožňuje implementovat svůj vlastní způsob odesílání oznámení. Jako příklad vás tento článek provede, jak získat oznámení o automatickém vypnutí z e-mailů pomocí Azure Logic Apps. Nejprve si rychle projdeme základní kroky, které povolou oznámení o automatickém vypnutí v testovacím prostředí.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Vytvoření aplikace logiky, která přijímá e-mailová oznámení
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) poskytuje mnoho out-of-the-box konektory, které usnadňuje integraci služby s jinými klienty, jako je Office 365 a twitter. Na vysoké úrovni lze kroky nastavení aplikace logiky pro e-mailové oznámení rozdělit do čtyř fází: 

- Vytvořte aplikaci logiky. 
- Nakonfigurujte předdefinovanou šablonu.
- Integrace s e-mailovým klientem
- Získejte adresu URL webhooku.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
Pokud chcete začít, vytvořte aplikaci logiky ve svém předplatném Azure pomocí následujících kroků:

1. Vyberte **+ Vytvořit prostředek** v levé nabídce, vyberte **Integrace**a vyberte **Aplikace logiky**. 

    ![Nová nabídka aplikace logiky](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na **stránce Logika – vytvořit** postupujte takto: 
    1. Zadejte **název** aplikace logiky.
    2. Vyberte **předplatné**Azure .
    3. Vytvořte novou **skupinu prostředků** nebo vyberte existující skupinu prostředků. 
    4. Vyberte **umístění** aplikace logiky. 

        ![Nová aplikace logiky - nastavení](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. V **oznámení**, vyberte **Přejít na zdroj** na oznámení. 

    ![Přejít k prostředku](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. V yberte **Návrhář aplikace logiky** v kategorii **Nástroje pro nasazení.**

    ![Vybrat požadavek/odpověď HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na stránce **HTTP Request-Response** vyberte **Použít tuto šablonu**. 

    ![Vybrat Možnost Použít tuto šablonu](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Zkopírujte následující json do části **Schéma JSON v těle požadavku:** 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Požadavek tělo JSON schéma](./media/devtest-lab-auto-shutdown/request-json.png)
7. V návrháři vyberte **+ Nový krok** a postupujte takto:
    1. Hledání **Office 365 Outlook – odeslání e-mailu**. 
    2. Vyberte **Odeslat e-mail** z **akce**. 
    
        ![Možnost Odeslat e-mail](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Vyberte **Přihlásit** se, abyste se přihlásili ke svému e-mailovému účtu. 
    4. Vyberte pole **TO** a zvolte vlastníka.
    5. Vyberte **PŘEDMĚT**a zadejte předmět e-mailového oznámení. Například: "Vypnutí počítače vmName pro laboratoř: labName."
    6. Vyberte **BODY**a definujte obsah těla pro e-mailové oznámení. Například: "vmName je naplánováno vypnutí za 15 minut. Toto vypnutí přeskočte kliknutím na: URL. Zpoždění vypnutí o hodinu: delayUrl60. Zpoždění vypnutí o 2 hodiny: delayUrl120."

        ![Požadavek tělo JSON schéma](./media/devtest-lab-auto-shutdown/email-options.png)
1. Na panelu nástrojů vyberte **Uložit**. Nyní můžete zkopírovat **adresu URL HTTP POST**. Vyberte tlačítko kopírovat, chcete-li zkopírovat adresu URL do schránky. 

    ![Adresa URL webového háku](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Konfigurace automatického vypnutí pro výpočetní virtuální počítače

1. Na stránce **Virtuální počítač** vyberte v levé nabídce **automatické vypnutí.** 
2. Na stránce Automatické vypnutí tuto zásadu povolte **výběrem** **možnosti Zapnuto** a Chcete-li ji **zakázat.**
3. Pokud povolíte tuto zásadu, zadejte **čas** (a **časové pásmo),** ve kterém by měl být virtuální vír vypnut.
4. Zadejte **ano** nebo **ne** pro možnost odeslat oznámení 30 minut před zadaným časem automatického vypnutí. Pokud zvolíte **Ano**, zadejte koncový bod adresy URL webhooku nebo e-mailovou adresu určující, kam má být oznámení odesláno nebo odesláno. Uživatel obdrží oznámení a je mu dána možnost odložit vypnutí. Další informace naleznete v části [Oznámení.](#notifications) 
9. Vyberte **Uložit**.

    ![Konfigurace automatického vypnutí pro výpočetní virtuální počítač](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

## <a name="next-steps"></a>Další kroky
Informace o tom, jak nastavit všechny zásady, najdete [v tématu Definování zásad testovacího prostředí v laboratořích Azure DevTest .](devtest-lab-set-lab-policy.md)

