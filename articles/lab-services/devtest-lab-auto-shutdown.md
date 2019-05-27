---
title: Správa zásad autoshutdown v Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak nastavit autoshutdown zásad pro testovací prostředí tak, aby virtuální počítače automaticky ukončí po jejich nejsou používány.
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
ms.date: 05/17/2019
ms.author: spelluru
ms.openlocfilehash: 9adf8dd4a5a3c469ed130b29308a0d828aee40bf
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873987"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>Správa zásad autoshutdown pro testovací prostředí ve službě Azure DevTest Labs
Azure DevTest Labs umožňuje řízení nákladů a minimalizace plýtvání v vaše testovací prostředí tím, že spravuje zásady (nastavení) pro každý testovací prostředí. V tomto článku se dozvíte, jak nakonfigurovat zásady autoshutdown pro účet testovacího prostředí a nastavení autoshutdown pro testovací prostředí v účtu testovacího prostředí. Chcete-li zobrazit nastavení všech zásad testovacího prostředí, najdete v článku [definice zásad testovacího prostředí ve službě Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>Nastavení automatického vypnutí zásad testovacího prostředí
Jako vlastník testovacího prostředí můžete nakonfigurovat plán vypnutí pro všechny virtuální počítače ve vaší laboratoři. Díky tomu dá ušetřit z počítače, které nejsou používány spuštěno (nečinné). Můžou vynutit zásadu vypínání na všechny virtuální počítače testovacího prostředí centrálně, ale také uložit uživatelům testovacího prostředí úsilí z nastavení plánu pro jejich jednotlivých počítačů. Tato funkce umožňuje nastavit zásady v testovacím plánu od nabídky žádný ovládací prvek pro úplné řízení ke uživatelům testovacího prostředí. Jako vlastník testovacího prostředí můžete nakonfigurovat tyto zásady podle následujících kroků:

1. Na domovské stránce testovacího prostředí, vyberte **konfigurace a zásad**.
2. Vyberte **automatické vypnutí zásad** v **plány** část v levé nabídce.
3. Vyberte jednu z možností. Následující části poskytují další podrobnosti o těchto možnostech: Nastavení zásad platí pouze pro nové virtuální počítače vytvořené v testovacím prostředí a ne na už existující virtuální počítače. 

    ![Automatické vypnutí možnosti zásad](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>Konfigurace nastavení automatického vypnutí
Zásada autoshutdown pomáhá minimalizovat plýtvání testovacího prostředí, neboť umožňuje určit čas, který se vypnout virtuální počítače v tomto testovacím prostředí.

K zobrazení (a změna) zásad pro testovací prostředí, postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte požadované prostředí.   
4. Vyberte **konfigurace a zásad**.

    ![Podokno nastavení zásad](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. Cvičení **konfigurace a zásad** vyberte **automatického vypínání** pod **plány**.
   
    ![Autoshutdown](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Vyberte **na** pro tuto zásadu povolit a **vypnout** ho zakážete.
7. Pokud tuto zásadu povolit, zadejte čas (a časové pásmo), vypnutí všech virtuálních počítačích v aktuálním testovacím prostředí.
8. Zadejte **Ano** nebo **ne** pro možnost poslat oznámení 15 minut před časem zadané autoshutdown. Pokud se rozhodnete **Ano**, zadejte koncový bod adresy URL webhooku nebo e-mailovou adresu zadáte, ve kterém chcete oznámení k odeslání nebo odeslání. Uživatel obdrží oznámení a je zadána možnost zpoždění vypnutí počítače. Další informace najdete v tématu [oznámení](#notifications) oddílu. 
9. Vyberte **Uložit**.

    Ve výchozím nastavení, jakmile bude povoleno, tyto zásady platí pro všechny virtuální počítače v aktuálním testovacím prostředí. Tato nastavení odebrat z konkrétní virtuální počítač, otevřete panel pro správu Virtuálního počítače a změňte jeho **Autoshutdown** nastavení.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Uživatel nastaví plánu a můžete je zrušit
Pokud nastavíte testovacího prostředí na tuto zásadu, uživatelé testovacího prostředí můžete přepsat nebo vyjádřit výslovný nesouhlas v testovacím plánu. Tato možnost umožňuje uživatelé testovacího prostředí plnou kontrolu nad plán vypnutí automatického jejich virtuálních počítačů. Uživatelé testovacího prostředí naleznete v tématu žádné změny v jejich stránce plán vypnutí virtuálního počítače automaticky.

![Automatické vypnutí zásad možnost - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Uživatel nastaví plán a nelze odhlásit
Pokud nastavíte testovacího prostředí na tuto zásadu, uživatelé testovacího prostředí můžete přepsat v testovacím plánu. Ale že nemohou výslovně nesouhlasit automatické vypnutí zásad. Tato možnost zajišťuje, každý počítač ve vaší laboratoři je v rámci plánu automatické vypnutí. Uživatelé testovacího prostředí můžete aktualizovat plán vypnutí automatického jejich virtuálních počítačů a nastavení oznámení o vypnutí.

![Automatické vypnutí zásad možnost - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Uživatel nemá žádnou kontrolu nad plánu nastaveného správcem testovacího prostředí
Pokud nastavíte testovacího prostředí na tuto zásadu, uživatelé testovacího prostředí nelze přepsat nebo vyjádřit výslovný nesouhlas v testovacím plánu. Tato možnost nabízí správce testovacího prostředí úplnou kontrolu nad plán pro každý počítač v testovacím prostředí. Uživatelé testovacího prostředí můžete nastavit jenom automatické vypnutí oznámení pro své virtuální počítače.

![Automatické vypnutí zásad možnost - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Oznámení
Jakmile autoshutdown nastavit vlastníkem testovacího prostředí, se budou odesílat oznámení uživatelům testovacího prostředí 15 minut před autoshutdown aktivovat v případě, že některé z jejich virtuálních počítačů bude mít vliv. Tato možnost poskytuje uživatelé testovacího prostředí a využijte šanci uložte svou práci před vypnutí. Oznámení obsahuje také odkazy pro každý virtuální počítač pro následující akce:

- Pro tuto chvíli přeskočit autoshutdown
- Připomenout znovu autoshutdown pro hodinu nebo dvě hodiny tak, že můžete pokračovat v práci na virtuálním počítači.

Oznámení se posílá prostřednictvím nakonfigurované webový koncový bod připojení nebo podle vlastníků testovacího prostředí v nastavení autoshutdown zadané e-mailovou adresu. Webhooky umožňují vytvářet nebo nastavení integrace, které se přihlásit k odběru určité události. Když některá z těchto událostí se aktivuje, DevTest Labs pošle datovou část HTTP POST na nakonfigurovanou adresu URL se webhook. Další informace o webhooků najdete v tématu [vytvoření webhooku nebo funkce rozhraní API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Doporučujeme použít webhooků, protože se hojně podporuje různé aplikace (například Slack, Azure Logic Apps a atd.) a umožňuje implementovat vlastní způsob pro odesílání oznámení. Jako příklad Tento článek vás provede získání autoshutdown oznámení z e-mailů pomocí Azure Logic Apps. Nejprve můžeme rychle projít základními kroky ke zprovoznění autoshutdown oznámení ve vaší laboratoři.   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>Vytvoření aplikace logiky, která bude přijímat e-mailových oznámení
[Služba Azure Logic Apps](../logic-apps/logic-apps-overview.md) poskytuje celou řadu konektorů out-of-the-box, které umožňuje snadno integrovat službu s ostatními klienty, jako je Office 365 a twitter. Na vysoké úrovni kroky k nastavení aplikace logiky pro e-mailové oznámení můžete rozdělit do čtyř fází: 

- Vytvoření aplikace logiky. 
- Konfigurovat předdefinované šablony.
- Integrace s e-mailového klienta
- Získáte adresu URL Webhooku.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
Abyste mohli začít, vytvořte aplikaci logiky ve vašem předplatném Azure pomocí následujících kroků:

1. Vyberte **+ vytvořit prostředek** v nabídce vlevo vyberte **integrace**a vyberte **aplikace logiky**. 

    ![Nové nabídce aplikace logiky](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na **aplikace logiky – vytvořit** stránce, postupujte podle těchto kroků: 
    1. Zadejte **název** pro aplikaci logiky.
    2. Vyberte své **předplatné** Azure.
    3. Vytvořte nový **skupiny prostředků** nebo vyberte existující skupinu prostředků. 
    4. Vyberte **umístění** pro aplikaci logiky. 

        ![Aplikace logiky – nastavení](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. V **oznámení**vyberte **přejít k prostředku** na oznámení. 

    ![Přejít k prostředku](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Vyberte **návrhář aplikace logiky** pod **nástroje pro nasazení** kategorie.

    ![Vyberte žádost/odpověď HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na **odpověď na požadavek HTTP** stránce **pomocí této šablony**. 

    ![Vyberte možnost použít tuto šablonu](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Zkopírujte následující kód JSON do **požádat o schéma JSON hlavní části** části: 

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
    
    ![Schéma JSON hlavní části požadavku](./media/devtest-lab-auto-shutdown/request-json.png)
7. Vyberte **+ nový krok** v návrháři a postupujte podle těchto kroků:
    1. Vyhledejte **Office 365 Outlook – odeslat e-mail**. 
    2. Vyberte **odeslat e-mailu** z **akce**. 
    
        ![Odeslání e-mailu – možnost](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Vyberte **přihlášení** pro přihlášení do vaší e-mailový účet. 
    4. Vyberte **na** pole a tlačítko vlastníka.
    5. Vyberte **subjektu**a zadejte předmět e-mailové oznámení. Příklad: "Vypnutí počítače vmName pro testovací prostředí: labName."
    6. Vyberte **tělo**a definujte obsah textu e-mailové oznámení. Příklad: "vmName je naplánováno vypnutí za 15 minut. Přeskočte kliknutím na toto vypnutí: ADRESA URL. Zpoždění vypnutí hodinu: delayUrl60. Zpoždění vypnutí další 2 hodiny: delayUrl120. "

        ![Schéma JSON hlavní části požadavku](./media/devtest-lab-auto-shutdown/email-options.png)
1. Na panelu nástrojů vyberte **Uložit**. Teď můžete zkopírovat **adresa URL operace HTTP POST**. Vyberte tlačítko Kopírovat zkopírujte adresu URL do schránky. 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Další postup
Zjistěte, jak nastavit všechny zásady, najdete v článku [definice zásad testovacího prostředí ve službě Azure DevTest Labs](devtest-lab-set-lab-policy.md).
