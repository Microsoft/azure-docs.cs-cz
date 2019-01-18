---
title: Automatické škálování prostředků Azure na základě údajů o výkonu nebo časového plánu
description: Vytvoření nastavení automatického škálování pro plán služby App Service s použitím dat metriky a časového plánu
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.component: autoscale
ms.openlocfilehash: 5cbe984e81b429b8d9d548015a5cd7ca33a0a71a
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382711"
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Vytvoření nastavení automatického škálování prostředků Azure na základě údajů o výkonu nebo časového plánu

Nastavení automatického škálování umožňují přidávat nebo odebírat instance služby na základě přednastavených podmínek. Tato nastavení je možné vytvářet prostřednictvím portálu. Tato metoda poskytuje uživatelské rozhraní v prohlížeči pro vytváření a konfigurování nastavení automatického škálování. 

V tomto kurzu se naučíte: 
> [!div class="checklist"]
> * Vytvořit webovou aplikaci a plán služby App Service
> * Konfigurovat pravidla pro automatické škálování na méně nebo více instancí na základě počtu požadavků obdrženého webovou aplikací
> * Aktivovat akci škálování na více instancí a sledovat zvyšování počtu instancí
> * Aktivovat akci škálování na méně instancí a sledovat snižování počtu instancí
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Vytvoření webové aplikace a plánu služby App Service
1. V levém navigačním podokně klikněte na možnost **Vytvořit prostředek**.
2. Vyhledejte položku *Webová aplikace*, klikněte na ni a pak klikněte na **Vytvořit**.
3. Vyberte název aplikace, například *MyTestScaleWebApp*. Vytvořit novou skupinu prostředků * myResourceGroup "nebo umístit do skupiny prostředků podle svého výběru.

Během několika minut by mělo proběhnout zřízení vašich prostředků. Ve zbývající části tohoto kurzu budete používat webovou aplikaci a odpovídající plán služby App Service.

   ![Vytvoření nové služby App Service v portálu](./media/tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Přechod k nastavením automatického škálování
1. V levém navigačním podokně vyberte možnost **Sledovat**. Po načtení stránky vyberte kartu **Automatické škálování**.
2. Je tam uveden seznam prostředků v rámci vašeho předplatného, které podporují automatické škálování. Najděte plán služby App Service vytvořený dříve v tomto kurzu a klikněte na něj.

    ![Přechod k nastavením automatického škálování](./media/tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. V nastavení automatického škálování klikněte na tlačítko **Povolit automatické škálování**.

Následujících několik kroků vás provede vyplněním obrazovky automatického škálování tak, aby nastavení vypadalo podobně jako na následujícím obrázku:

   ![Uložení nastavení automatického škálování](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

 ## <a name="configure-default-profile"></a>Konfigurace výchozího profilu
1. Zadejte **Název** pro nastavení automatického škálování.
2. Ve výchozím profilu zkontrolujte, že je možnost **Režim škálování** nastavená na hodnotu Škálovat na konkrétní počet instancí.
3. Nastavte počet instancí **1**. Toto nastavení zajistí, že když žádný jiný profil nebude aktivní nebo se nebude uplatňovat, výchozí profil obnoví počet instancí 1.

  ![Přechod k nastavením automatického škálování](./media/tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Vytvoření profilu opakování

1. Klikněte na odkaz **Přidat podmínku škálování** pod výchozím profilem.

2. Upravte **Název** tohoto profilu na text Monday to Friday profile (Profil od pondělí do pátku).

3. Zkontrolujte, že je možnost **Režim škálování** nastavená na hodnotu Škálovat podle metriky.

4. V části **Limity instancí** nastavte **Minimum** na hodnotu 1, **Maximum** na hodnotu 2 a **Výchozí** na hodnotu 1. Toto nastavení zajistí, aby tento profil neprováděl automatické škálování plánu služby na méně než 1 instanci nebo více než 2 instance. Pokud profil nemá dostatek dat pro provedení rozhodnutí, použije výchozí počet instancí (v tomto případě 1).

5. V části **Plán** vyberte Opakovat konkrétní dny.

6. Nastavte profil tak, aby se opakoval od pondělí do pátku od 9:00 PST do 18:00 PST. Toto nastavení zajistí, že tento profil bude aktivní a použitelný jen od 9:00 do 18 00 od pondělí do pátku. Mimo tuto dobu se pro nastavení automatického škálování používá profil Výchozí.

## <a name="create-a-scale-out-rule"></a>Vytvoření pravidla pro škálování na více instancí

1. V profilu Monday to Friday profile.

2. Klikněte na odkaz **Přidat pravidlo**.

3. Nastavte možnost **Zdroj metriky** na hodnotu jiný prostředek. Nastavte možnost **Typ prostředku** na hodnotu App Services a jako **prostředek** nastavte webovou aplikaci vytvořenou dříve v tomto kurzu.

4. Nastavte možnost **Časová agregace** na hodnotu Celkem, možnost **Název metriky** na hodnotu Požadavky a možnost **Statistika agregačního intervalu** na hodnotu Součet.

5. Nastavte možnost **Operátor** na hodnotu Větší než, možnost **Prahová hodnota** na hodnotu 10 a možnost **Doba trvání** na hodnotu 5 minut.

6. Vyberte pro možnost **Operace** hodnotu Zvýšit počet o, pro možnost **Počet instancí** hodnotu 1 a pro možnost **Obnovení funkčnosti** hodnotu 5 minut.

7. Klikněte na tlačítko **Přidat**.

Toto pravidlo zajišťuje, že pokud vaše webová aplikace obdrží více než 10 požadavků během 5 minut nebo kratší doby, přidá se do plánu služby App Service jedna další instance, aby bylo možné spravovat zatížení.

   ![Vytvoření pravidla pro škálování na více instancí](./media/tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Vytvoření pravidla pro škálování na méně instancí
Doporučujeme vám vždy mít pro pravidlo pro škálování na více instancí nastaveno i pravidlo pro škálování na méně instancí. Pokud máte obě, je zajištěno, že se vaše prostředky nebudou zřizovat v nadměrném množství. Zřizování v nadměrném množství znamená, že máte spuštěných víc instancí, než pro zpracování aktuální zátěže potřebujete. 

1. V profilu Monday to Friday profile.

2. Klikněte na odkaz **Přidat pravidlo**.

3. Nastavte možnost **Zdroj metriky** na hodnotu jiný prostředek. Nastavte možnost **Typ prostředku** na hodnotu App Services a jako **prostředek** nastavte webovou aplikaci vytvořenou dříve v tomto kurzu.

4. Nastavte možnost **Časová agregace** na hodnotu Celkem, možnost **Název metriky** na hodnotu Požadavky a možnost **Statistika agregačního intervalu** na hodnotu Průměr.

5. Nastavte možnost **Operátor** na hodnotu Menší než, možnost **Prahová hodnota** na hodnotu 5 a možnost **Doba trvání** na hodnotu 5 minut.

6. Vyberte pro možnost **Operace** hodnotu Snížit počet o, pro možnost **Počet instancí** hodnotu 1 a pro možnost **Obnovení funkčnosti** hodnotu 5 minut.

7. Klikněte na tlačítko **Přidat**.

    ![Vytvoření pravidla pro škálování na méně instancí](./media/tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Uložte** nastavení automatického škálování.

    ![Uložení nastavení automatického škálování](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Aktivace akce škálování na více instancí
Aby se pro nastavení automatického škálování, které jste právě vytvořili, aktivoval stav škálování na více instancí, musí webová aplikace obdržet během doby kratší než 5 minut více než 10 požadavků.

1. Otevřete okno prohlížeče a přejděte k webové aplikaci, kterou jste vytvořili dříve v tomto kurzu. Adresu URL webové aplikace najdete na webu Azure Portal po přechodu k prostředku příslušné webové aplikace a kliknutí na tlačítko **Procházet** na kartě Přehled.

2. Rychle po sobě načtěte tuto stránku více než 10krát.

3. V levém navigačním podokně vyberte možnost **Sledovat**. Po načtení stránky přejděte na kartu **Automatické škálování**.

4. V seznamu vyberte plán služby App Service používaný v celém tomto kurzu.

5. V nastavení automatického škálování klikněte na kartu **Historie spuštění**.

6. Zobrazí se graf znázorňující počet instancí plánu služby App Service v čase.

7. Za několik minut by se počet instancí měl zvýšit z 1 na 2.

8. Pod grafem jsou uvedeny položky protokolu aktivit pro každou akci škálování provedenou na základě tohoto nastavení automatického škálování.

## <a name="trigger-scale-in-action"></a>Aktivace akce škálování na méně instancí
Stav škálování na méně instancí se pro příslušné nastavení automatického škálování aktivuje v případě, že webová aplikace obdrží během 10 minut méně než 5 požadavků. 

1. Ujistěte se, že se do vaší webové aplikace neodesílají žádné požadavky.

2. Načtěte web Azure Portal.

3. V levém navigačním podokně vyberte možnost **Sledovat**. Po načtení stránky přejděte na kartu **Automatické škálování**.

4. V seznamu vyberte plán služby App Service používaný v celém tomto kurzu.

5. V nastavení automatického škálování klikněte na kartu **Historie spuštění**.

6. Zobrazí se graf znázorňující počet instancí plánu služby App Service v čase.

7. Za několik minut by se počet instancí měl snížit z 2 na 1. Proces trvá aspoň 100 minut.  

8. Pod grafem je uvedena odpovídající sada položek protokolu aktivit pro každou akci škálování provedenou na základě tohoto nastavení automatického škálování.

    ![Zobrazení akcí škálování na méně instancí](./media/tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte webovou aplikaci vytvořenou v tomto kurzu.

2. Na stránce prostředků klikněte na **Odstranit**, potvrďte odstranění zadáním textu **yes** do textového pole a pak klikněte na **Odstranit**.

3. Pak vyberte prostředek plánu služby App Service a klikněte na **Odstranit**.

4. Potvrďte odstranění zadáním textu **yes** do textového pole a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste:  
> [!div class="checklist"]
> * vytvořili webovou aplikaci a plán služby App Service,
> * konfigurovali pravidla pro automatické škálování na méně nebo více instancí na základě počtu požadavků obdrženého webovou aplikací,
> * aktivovali akci škálování na více instancí a sledovali zvyšování počtu instancí,
> * aktivovali akci škálování na méně instancí a sledovali snižování počtu instancí,
> * vyčistili prostředky.


Další informace o nastaveních automatického škálování najdete v [přehledu automatického škálování](../../azure-monitor/platform/autoscale-overview.md).

> [!div class="nextstepaction"]
> [Archivace dat monitorování](tutorial-archive-data.md)
