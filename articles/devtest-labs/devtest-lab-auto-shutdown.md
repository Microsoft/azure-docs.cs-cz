---
title: Spravovat zásady pro automatické vypínání v Azure DevTest Labs | Microsoft Docs
description: Naučte se, jak nastavit zásady automatického vypnutí pro testovací prostředí, aby se virtuální počítače automaticky vypnuly, když se nepoužívají.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3532e4859bd63c81cb9b9cda973064e5ef8357ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320706"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Konfigurace automatické vypnutí testovacího prostředí a virtuálních počítačů COMPUTE v Azure DevTest Labs

Tento článek vysvětluje, jak nakonfigurovat nastavení pro automatické vypnutí testovacích virtuálních počítačů v DevTest Labs a výpočetních virtuálních počítačích. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Konfigurace automatické vypínání pro virtuální počítače v testovacím prostředí (DevTest Labs)
Azure DevTest Labs vám umožní řídit náklady a minimalizovat plýtvání v laboratoři tím, že spravují zásady (nastavení) pro každé testovací prostředí. V tomto článku se dozvíte, jak nakonfigurovat zásady pro automatické vypínání pro účet testovacího prostředí a nakonfigurovat nastavení pro automatické vypínání pro testovací prostředí v účtu testovacího prostředí. Pokud chcete zobrazit, jak nastavit všechny zásady testovacího prostředí, přečtěte si téma [Definování zásad testovacího prostředí v Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>Nastavení zásad automatického vypnutí pro testovací prostředí
Jako vlastník testovacího prostředí můžete nakonfigurovat plán vypnutí pro všechny virtuální počítače v testovacím prostředí. Díky tomu můžete ušetřit náklady ze spuštěných počítačů, které se nepoužívají (nečinné). Zásady vypnutí můžete vystavit na všech virtuálních počítačích v testovacím prostředí centrálně, ale také ukládat uživatele testovacího prostředí s úsilím od nastavení plánu pro jednotlivé počítače. Tato funkce umožňuje nastavit zásady pro plán testovacího prostředí od nabídky bez řízení k úplnému řízení uživatelům testovacího prostředí. Jako vlastník testovacího prostředí můžete nakonfigurovat tuto zásadu provedením následujících kroků:

1. Na domovské stránce testovacího prostředí vyberte **Konfigurace a zásady**.
2. V levé nabídce vyberte v části **plány** možnost **zásady automatického vypnutí** .
3. Vyberte jednu z možností. Následující části obsahují další podrobnosti o těchto možnostech: nastavení zásad se vztahuje pouze na nové virtuální počítače vytvořené v testovacím prostředí, nikoli na již existující virtuální počítače. 

    ![Možnosti automatického vypnutí zásad](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurovat nastavení automatického vypnutí
Zásady pro automatické vypínání pomáhají minimalizovat odpadní laboratoře tím, že vám umožní určit čas, kdy se virtuální počítače testovacího prostředí vypnuly.

Chcete-li zobrazit (a změnit) zásady pro testovací prostředí, postupujte podle následujících kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte požadované testovací prostředí.   
4. Vyberte **Konfigurace a zásady**.

    ![Podokno nastavení zásad](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. V podokně **Konfigurace a zásady** testovacího prostředí vyberte **automaticky vypínání** v části **plány**.
   
    ![Automatické vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Výběrem **zapnuto** tuto zásadu povolíte a **Off** zakážete.
7. Pokud tuto zásadu povolíte, zadejte čas (a časové pásmo) pro vypnutí všech virtuálních počítačů v aktuálním testovacím prostředí.
8. Zadejte **hodnotu yes** nebo **No** pro možnost odeslání oznámení 30 minut před zadaným časem automatické vypnutí. Pokud zvolíte **Ano**, zadejte koncový bod adresy URL Webhooku nebo e-mailovou adresu, která určuje, kam chcete oznámení publikovat nebo odeslat. Uživatel obdrží oznámení a získá možnost zpoždění vypnutí. Další informace najdete v části [oznámení](#notifications) . 
9. Vyberte **Uložit**.

    Ve výchozím nastavení platí, že když je tato zásada povolená, vztahuje se na všechny virtuální počítače v aktuálním testovacím prostředí. Pokud chcete toto nastavení odebrat z konkrétního virtuálního počítače, otevřete podokno pro správu virtuálního počítače a změňte jeho nastavení pro automatické **vypnutí** .
    
> [!NOTE]
> Pokud aktualizujete plán automatické vypínání pro testovací prostředí nebo konkrétní virtuální počítač v testovacím prostředí do 30 minut od aktuálního naplánovaného času, bude se aktualizovaná Doba vypnutí vztahovat k plánu příštího dne. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Uživatel nastaví plán a může se odhlásit.
Pokud nastavíte testovací prostředí na tuto zásadu, můžou uživatelé testovacího prostředí potlačit nebo odsouhlasit plán testovacího prostředí. Tato možnost uděluje uživatelům testovacího prostředí plnou kontrolu nad plánem automatického vypnutí svých virtuálních počítačů. Uživatelé testovacího prostředí uvidí na stránce plán automatického vypínání virtuálních počítačů žádné změny.

![Možnost automatického vypnutí zásady – 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Uživatel nastaví plán a nemůže se odhlásit.
Pokud nastavíte testovací prostředí na tuto zásadu, můžou uživatelé testovacího prostředí přepsat plán testovacího prostředí. Nemůžou ale odhlásit zásady automatického vypnutí. Tato možnost zajistí, že každý počítač v testovacím prostředí je v plánu automatického vypnutí. Uživatelé testovacího prostředí můžou aktualizovat plán automatického vypnutí svých virtuálních počítačů a nastavit oznámení o vypnutí.

![Možnost automatického vypnutí zásady – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Uživatel nemá žádnou kontrolu nad plánem nastaveným správcem testovacího prostředí.
Pokud nastavíte testovací prostředí na tuto zásadu, uživatelé testovacího prostředí nebudou moct přepsat nebo odhlásit plán testovacího prostředí. Tato možnost nabízí správci testovacího prostředí úplnou kontrolu nad plánem pro každý počítač v testovacím prostředí. Uživatelé testovacího prostředí můžou pro svoje virtuální počítače nastavit jenom oznámení o automatickém vypnutí.

![Možnost automatického vypnutí zásady – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Oznámení
Jakmile se automatické vypnutí nastaví vlastníkem testovacího prostředí, pošle se oznámení uživatelům testovacího prostředí 30 minut, než se aktivuje automatické vypnutí, pokud bude ovlivněn některý z jeho virtuálních počítačů. Tato možnost dává uživatelům testovacího prostředí možnost uložit práci před vypnutím. Oznámení také obsahuje odkazy na jednotlivé virtuální počítače pro následující akce:

- Přeskočit automatické vypínání pro tentokrát
- Automatické vypnutí se odhlásí po hodinu nebo 2 hodinách, aby mohli na virtuálním počítači dál pracovat.

Oznámení se odesílá prostřednictvím nakonfigurovaného koncového bodu webového zavěšení nebo e-mailové adresy zadané vlastníky testovacího prostředí v nastavení automatické vypnutí. Webhooky umožňují vytvářet nebo nastavovat integrace, které se přihlásí k odběru určitých událostí. Když se aktivuje jedna z těchto událostí, DevTest Labs pošle datovou část HTTP POST do konfigurované adresy URL Webhooku. Další informace o webhookech najdete v tématu [Vytvoření Webhooku nebo rozhraní API Azure Function](../azure-functions/functions-bindings-http-webhook.md). 

Doporučujeme používat Webhooky, protože jsou široce podporované různými aplikacemi (například časová rezerva, Azure Logic Apps a tak dále) a umožňují vám implementovat vlastní způsob odesílání oznámení. Tento článek vás seznámí s postupem, jak získat oznámení o automatickém vypnutí z e-mailů pomocí Azure Logic Apps. Nejdřív se rychle provedeme základními kroky, které vám umožní oznámení o automatickém vypnutí v testovacím prostředí.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Vytvoření aplikace logiky, která přijímá e-mailová oznámení
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) poskytuje spoustu předem připravených konektorů, které usnadňují integraci služby s ostatními klienty, jako je třeba Office 365 a Twitter. Na nejvyšší úrovni se kroky pro nastavení aplikace logiky pro e-mailové oznámení dají rozdělit do čtyř fází: 

- Vytvořte aplikaci logiky. 
- Nakonfigurujte předdefinovanou šablonu.
- Integrace s vaším e-mailovým klientem
- Získejte adresu URL Webhooku.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky
Začněte tím, že ve svém předplatném Azure vytvoříte aplikaci logiky pomocí následujících kroků:

1. V nabídce vlevo vyberte **+ vytvořit prostředek** , vyberte **integrace**a pak vyberte **Aplikace logiky**. 

    ![Nová nabídka aplikace logiky](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na stránce **Aplikace logiky – vytvořit** proveďte následující kroky: 
    1. Zadejte **název** aplikace logiky.
    2. Vyberte své **předplatné**Azure.
    3. Vytvořte novou **skupinu prostředků** nebo vyberte existující skupinu prostředků. 
    4. Vyberte **umístění** pro aplikaci logiky. 

        ![Nová aplikace logiky – nastavení](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. V okně **oznámení**vyberte v oznámení **Přejít na prostředek** . 

    ![Přejít k prostředku](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. V kategorii **Nástroje nasazení** vyberte **Návrhář aplikace logiky** .

    ![Vybrat požadavek a odpověď HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na stránce **požadavek HTTP – odpověď** vyberte **použít tuto šablonu**. 

    ![Vyberte možnost použít tuto šablonu.](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Zkopírujte následující kód JSON do části **schéma JSON textu žádosti** : 

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
    
    ![Snímek obrazovky zobrazující "schéma JSON textu žádosti".](./media/devtest-lab-auto-shutdown/request-json.png)
7. V návrháři vyberte **+ Nový krok** a proveďte tyto kroky:
    1. Vyhledejte **Office 365 Outlook – odeslání e-mailu**. 
    2. Vyberte **Odeslat e-mail** z **akcí**. 
    
        ![Možnost odeslání e-mailu](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Vyberte **Přihlásit** se a přihlaste se k e-mailovému účtu. 
    4. Vyberte pole **do** a zvolte Owner (Vlastník).
    5. Vyberte **Předmět**a zadejte předmět e-mailového oznámení. Příklad: "vypnutí počítače vmName pro testovací prostředí: labName".
    6. Vyberte **text**a definujte obsah textu e-mailového oznámení. Příklad: "vmName se naplánovalo vypnutí za 15 minut. Toto vypnutí přeskočte kliknutím na: adresa URL. Odložit vypnutí po hodinu: delayUrl60. Odložit vypnutí na 2 hodiny: delayUrl120. "

        ![Schéma JSON pro tělo požadavku](./media/devtest-lab-auto-shutdown/email-options.png)
1. Na panelu nástrojů vyberte **Uložit**. Nyní můžete zkopírovat **adresu URL post protokolu HTTP**. Kliknutím na tlačítko Kopírovat zkopírujte adresu URL do schránky. 

    ![Adresa URL Webhooku](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Konfigurace automatické vypnutí výpočetních virtuálních počítačů

1. Na stránce **virtuální počítač** vyberte v nabídce vlevo v části **operace** možnost **Automatické vypnutí** . 
2. Na stránce **Automatické vypínání** vyberte **zapnuto** , **Pokud chcete tuto zásadu Povolit a zakázat** .
3. Pokud tuto zásadu povolíte, zadejte **čas** (a **časové pásmo**), ve kterém se má virtuální počítač vypnout.
4. Zadejte **hodnotu yes** nebo **No** pro možnost odeslání oznámení 30 minut před zadaným časem automatické vypnutí. Pokud zvolíte **Ano**, zadejte koncový bod adresy URL Webhooku nebo e-mailovou adresu, která určuje, kam chcete oznámení publikovat nebo odeslat. Uživatel obdrží oznámení a získá možnost zpoždění vypnutí. Další informace najdete v části [oznámení](#notifications) . 
9. Vyberte **Uložit**.

    ![Konfigurace automatické vypnutí pro výpočetní virtuální počítač](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Zobrazení protokolů aktivit pro aktualizace automatického vypnutí
Po aktualizaci nastavení automatické vypnutí se zobrazí aktivita zaznamenaná v protokolu aktivit virtuálního počítače. 

1. V [Azure Portal](https://portal.azure.com)přejděte na domovskou stránku svého virtuálního počítače.
2. V nabídce vlevo vyberte **Protokol aktivit** . 
3. Odeberte **prostředek: mycomputevm** z filtrů.
3. Potvrďte, že se v protokolu aktivit zobrazila operace **Přidat nebo upravit plány** . Pokud ho nevidíte, počkejte na nějakou dobu a aktualizujte protokol aktivit.

    ![Položka protokolu aktivit](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. Vyberte operaci **Přidat nebo upravit plány** a na stránce **Souhrn** se zobrazí následující informace:

    - Název operace (Přidat nebo upravit plány)
    - Datum a čas, kdy bylo nastavení automatické vypnutí aktualizováno.
    - E-mailová adresa uživatele, který nastavení aktualizoval 

        ![Souhrn položky protokolu aktivit](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. V případě, že na stránce **Přidat nebo upravit plány** přejdete na kartu **historie změn** , uvidíte historii změn nastavení. V následujícím příkladu se čas vypnutí změnil z 10. dubna 2020 na 15:18:47 EST. A nastavení bylo zakázáno v 15:25:09 EST. 

    ![Protokol aktivit – historie změn](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. Chcete-li zobrazit další podrobnosti o operaci, přepněte na kartu **JSON** na stránce **Přidat nebo upravit plány** .

## <a name="next-steps"></a>Další kroky
Informace o tom, jak nastavit všechny zásady, najdete v tématu [Definování zásad testovacího prostředí v Azure DevTest Labs](devtest-lab-set-lab-policy.md).
