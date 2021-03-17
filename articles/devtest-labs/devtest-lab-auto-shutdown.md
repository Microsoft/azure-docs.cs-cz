---
title: Správa zásad pro automatické vypínání v Azure DevTest Labs a výpočetních virtuálních počítačů | Microsoft Docs
description: Přečtěte si, jak nastavit zásady automatického vypnutí pro testovací prostředí, aby se virtuální počítače automaticky vypnuly, když se nepoužívají.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cd7974580ea30c9d0591c88380a4e626711bad1e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318984"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Konfigurace automatické vypnutí testovacího prostředí a virtuálních počítačů COMPUTE v Azure DevTest Labs

Tento článek vysvětluje, jak nakonfigurovat nastavení pro automatické vypnutí testovacích virtuálních počítačů v DevTest Labs a výpočetních virtuálních počítačích.

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Konfigurace automatické vypínání pro virtuální počítače v testovacím prostředí (DevTest Labs)

Azure DevTest Labs vám umožní řídit náklady a minimalizovat plýtvání v laboratoři tím, že spravují zásady (nastavení) pro každé testovací prostředí. V tomto článku se dozvíte, jak nakonfigurovat zásady pro automatické vypínání pro testovací prostředí.  Také ukazuje, jak nakonfigurovat nastavení pro automatické vypínání pro virtuální počítač v testovacím prostředí. Pokud chcete zobrazit, jak nastavit všechny zásady testovacího prostředí, přečtěte si téma [Definování zásad testovacího prostředí v Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-autoshutdown-policy-for-a-lab"></a>Nastavení zásad pro automatické vypínání pro testovací prostředí

Jako vlastník testovacího prostředí můžete nakonfigurovat plán vypnutí pro všechny virtuální počítače v testovacím prostředí. Díky tomu můžete ušetřit náklady ze spuštěných počítačů, které se nepoužívají (nečinné). Zásady vypnutí můžete vystavit na všech virtuálních počítačích v testovacím prostředí centrálně a také ušetřit uživatele v testovacím prostředí tím, že nasadíte plán pro jednotlivé počítače. Tato funkce umožňuje nastavit zásady v plánu testovacího prostředí v rozsahu od povolení uživatelům testovacího prostředí, aby měli plnou kontrolu nad časovým plánem vypnutí tohoto virtuálního počítače bez kontroly nad jeho vypnutím. Jako vlastník testovacího prostředí můžete nakonfigurovat tuto zásadu provedením následujících kroků:

1. Na domovské stránce testovacího prostředí vyberte **Konfigurace a zásady**.
2. V levé nabídce vyberte v části **plány** možnost **zásady automatického vypnutí** .
3. Vyberte jednu z možností. Následující části obsahují další podrobnosti o těchto možnostech:

    ![Možnosti automatického vypnutí zásad](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

> [!IMPORTANT]
> Změny zásad vypnutí se vztahují jenom na nové virtuální počítače vytvořené v testovacím prostředí, a ne na už existující virtuální počítače.

### <a name="configure-autoshutdown-settings"></a>Konfigurace nastavení pro automatické vypnutí

Zásady pro automatické vypínání pomáhají minimalizovat odpadní laboratoře tím, že vám umožní určit čas, kdy se virtuální počítače v testovacím prostředí vypnou.

Chcete-li zobrazit nebo změnit zásady pro testovací prostředí, postupujte podle následujících kroků:

1. Na domovské stránce testovacího prostředí vyberte **Konfigurace a zásady**.
2. V levé nabídce vyberte **Automatické vypnutí** v části **plány** .
3. Výběrem **zapnuto** tuto zásadu povolíte a **Off** zakážete.
     ![Podrobnosti automatického vypnutí](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
4. Pokud tuto zásadu povolíte, zadejte čas (a časové pásmo) pro vypnutí všech virtuálních počítačů v aktuálním testovacím prostředí.
5. Zadejte **hodnotu yes** nebo **No** pro možnost odeslání oznámení 30 minut před zadaným časem pro automatické vypnutí. Pokud zvolíte **Ano** , zadejte koncový bod adresy URL Webhooku nebo e-mailovou adresu, která určuje, kam chcete oznámení publikovat nebo odeslat. Uživatel obdrží oznámení a získá možnost zpoždění vypnutí. Další informace najdete v části [oznámení](#notifications) .
6. Vyberte **Uložit**.

    Ve výchozím nastavení platí, že když je tato zásada povolená, vztahuje se na všechny virtuální počítače v aktuálním testovacím prostředí. Pokud chcete toto nastavení odebrat z konkrétního virtuálního počítače, otevřete podokno pro správu virtuálního počítače a změňte jeho nastavení **automatického vypnutí** .

> [!NOTE]
> Pokud aktualizujete plán automatické vypínání pro testovací prostředí nebo konkrétní virtuální počítač v testovacím prostředí do 30 minut od aktuálního naplánovaného času, bude se aktualizovaná Doba vypnutí vztahovat k plánu příštího dne.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Uživatel nastaví plán a může se odhlásit.

Pokud nastavíte testovací prostředí na tuto zásadu, můžou uživatelé testovacího prostředí potlačit nebo odsouhlasit plán testovacího prostředí. Tato možnost uděluje uživatelům testovacího prostředí plnou kontrolu nad plánem automatického vypnutí svých virtuálních počítačů. Uživatelé testovacího prostředí uvidí na stránce plán automatického vypínání virtuálních počítačů žádné změny.

![Možnost automatického vypnutí zásady – 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Uživatel nastaví plán a nemůže se odhlásit.

Pokud nastavíte testovací prostředí na tuto zásadu, můžou uživatelé testovacího prostředí přepsat plán testovacího prostředí. Nemůžou se ale odhlásit ze zásad pro automatické vypnutí. Tato možnost zajistí, že každý počítač v testovacím prostředí je v plánu automatické vypnutí. Uživatelé testovacího prostředí můžou aktualizovat plán pro automatické vypnutí svých virtuálních počítačů a nastavit oznámení o vypnutí.

![Možnost automatického vypnutí zásady – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Uživatel nemá žádnou kontrolu nad plánem nastaveným správcem testovacího prostředí.

Pokud nastavíte testovací prostředí na tuto zásadu, uživatelé testovacího prostředí nebudou moct přepsat nebo odhlásit plán testovacího prostředí. Tato možnost nabízí správci testovacího prostředí úplnou kontrolu nad plánem pro každý počítač v testovacím prostředí. Uživatelé testovacího prostředí můžou pro svoje virtuální počítače nastavit jenom oznámení o automatickém vypnutí.

![Možnost automatického vypnutí zásady – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Konfigurace automatické vypnutí výpočetních virtuálních počítačů

1. Na stránce **virtuální počítač** vyberte v nabídce vlevo v části **operace** možnost **Automatické vypnutí** .
2. Na stránce **Automatické vypínání** vyberte **zapnuto** , **Pokud chcete tuto zásadu Povolit a zakázat** .
3. Pokud tuto zásadu povolíte, zadejte **čas** (a **časové pásmo** ), ve kterém se má virtuální počítač vypnout.
4. Vyberte **Ano** nebo **ne** pro možnost Odeslat oznámení 30 minut před zadanou dobu pro automatické vypnutí. Pokud zvolíte **Ano** , zadejte koncový bod adresy URL Webhooku nebo e-mailovou adresu, která určuje, kam chcete oznámení publikovat nebo odeslat. Uživatel obdrží oznámení a získá možnost zpoždění vypnutí. Další informace najdete v části [oznámení](#notifications) .
5. Vyberte **Uložit**.

    ![Konfigurace automatického vypnutí pro výpočetní virtuální počítač](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Zobrazení protokolů aktivit pro aktualizace automatického vypnutí

Když aktualizujete nastavení automatické vypnutí, zobrazí se aktivita zaznamenaná v protokolu aktivit virtuálního počítače.

1. V [Azure Portal](https://portal.azure.com)přejděte na domovskou stránku svého virtuálního počítače.
2. V nabídce vlevo vyberte **Protokol aktivit** .
3. Odeberte **prostředek: mycomputevm** z filtrů.
4. Potvrďte, že se v protokolu aktivit zobrazila operace **Přidat nebo upravit plány** . Pokud ho nevidíte, počkejte na nějakou dobu a aktualizujte protokol aktivit.

    ![Položka protokolu aktivit](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
5. Vyberte operaci **Přidat nebo upravit plány** a na stránce **Souhrn** se zobrazí následující informace:

    - Název operace (Přidat nebo upravit plány)
    - Datum a čas, kdy bylo nastavení automatické vypnutí aktualizováno.
    - E-mailová adresa uživatele, který nastavení aktualizoval

        ![Souhrn položky protokolu aktivit](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
6. V případě, že na stránce **Přidat nebo upravit plány** přejdete na kartu **historie změn** , uvidíte historii změn nastavení. V následujícím příkladu se čas vypnutí změnil z 10. dubna 2020 na 15:18:47 EST. A nastavení bylo zakázáno v 15:25:09 EST.

    ![Protokol aktivit – historie změn](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
7. Chcete-li zobrazit další podrobnosti o operaci, přepněte na kartu **JSON** na stránce **Přidat nebo upravit plány** .

## <a name="notifications"></a>Oznámení

Po nakonfigurovaném automatickém vypnutí budou oznámení odeslána uživatelům testovacího prostředí 30 minut, než se aktivuje automatické vypnutí, pokud bude ovlivněn některý z jeho virtuálních počítačů. Tato možnost dává uživatelům testovacího prostředí možnost uložit práci před vypnutím. Oznámení také poskytuje odkazy pro každý virtuální počítač pro následující akce v případě, že někdo musí na svém virtuálním počítači dál pracovat.

- Přeskočit automatické vypínání pro tentokrát
- Odložit automatické vypnutí po hodinu
- Znovu připomenout automatické vypnutí po 2 hodiny

Pokud byl zadán Webhook, pošle se oznámení na adresu URL Webhooku.  Pokud se v nastavení automatické vypnutí zadala e-mailová adresa, pošle se na tuto e-mailovou adresu e-mail. Webhooky umožňují vytvářet nebo nastavovat integrace, které se přihlásí k odběru určitých událostí. Když se aktivuje jedna z těchto událostí, DevTest Labs pošle datovou část HTTP POST do konfigurované adresy URL Webhooku. Další informace o tom, jak reagovat na Webhooky, najdete v tématu [Azure Functions triggery http a Přehled vazeb](../azure-functions/functions-bindings-http-webhook.md) nebo [Přidání triggeru http pro Azure Logic Apps](../connectors/connectors-native-http.md#add-an-http-trigger).

Doporučujeme používat Webhooky, protože jsou široce podporované různými aplikacemi, jako je Azure Logic Apps a časová rezerva.  Webhooky umožňují implementovat vlastní způsob odesílání oznámení. Tento článek vás seznámí s postupem konfigurace oznámení o automatickém vypnutí k odeslání e-mailu vlastníkovi virtuálního počítače pomocí Azure Logic Apps. Nejdřív se rychle provedeme základními kroky, které vám umožní oznámení o automatickém vypnutí v testovacím prostředí.

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Vytvoření aplikace logiky, která přijímá e-mailová oznámení

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) poskytuje spoustu konektorů, které usnadňují integraci služby s ostatními klienty, jako je třeba Office 365 a Twitter. Na nejvyšší úrovni se kroky pro nastavení aplikace logiky pro e-mailové oznámení dají rozdělit do čtyř fází:

- Vytvořte aplikaci logiky.
- Nakonfigurujte předdefinovanou šablonu.
- Integrace s vaším e-mailovým klientem
- Získejte adresu URL Webhooku.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Začněte tím, že ve svém předplatném Azure vytvoříte aplikaci logiky pomocí následujících kroků:

1. V nabídce vlevo vyberte **+ vytvořit prostředek** , vyberte **integrace** a pak vyberte **Aplikace logiky**.

    ![Nová nabídka aplikace logiky](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na stránce **Aplikace logiky – vytvořit** proveďte následující kroky:
    1. Zadejte **název** aplikace logiky.
    2. Vyberte své **předplatné** Azure.
    3. Vytvořte novou **skupinu prostředků** nebo vyberte existující skupinu prostředků.
    4. Vyberte **umístění** pro aplikaci logiky.

        ![Nová aplikace logiky – nastavení](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. V okně **oznámení** vyberte v oznámení **Přejít na prostředek** .

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
            },
            "vmUrl": {
                "type": "string"
            },
            "minutesUntilShutdown": {
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
            "labName",
            "vmUrl",
            "minutesUntilShutdown"
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
    5. Vyberte **Předmět** a zadejte předmět e-mailového oznámení. Příklad: "vypnutí počítače vmName pro testovací prostředí: labName".
    6. Vyberte **text** a definujte obsah textu e-mailového oznámení. Příklad: "vmName se naplánovalo vypnutí za 15 minut. Toto vypnutí přeskočte kliknutím na: adresa URL. Odložit vypnutí po hodinu: delayUrl60. Odložit vypnutí na 2 hodiny: delayUrl120. "

        ![Schéma JSON pro tělo požadavku](./media/devtest-lab-auto-shutdown/email-options.png)
8. Na panelu nástrojů vyberte **Uložit**. Nyní můžete zkopírovat **adresu URL post protokolu HTTP**. Kliknutím na tlačítko Kopírovat zkopírujte adresu URL do schránky.

    ![Adresa URL Webhooku](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Další kroky

Informace o tom, jak nastavit všechny zásady, najdete v tématu [Definování zásad testovacího prostředí v Azure DevTest Labs](devtest-lab-set-lab-policy.md).
