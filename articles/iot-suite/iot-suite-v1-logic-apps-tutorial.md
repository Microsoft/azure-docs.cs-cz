---
title: Azure IoT Suite a Logic Apps | Dokumentace Microsoftu
description: Kurz předvádějící způsoby připojení Logic Apps do Azure IoT Suite pro obchodní proces.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 4a1db86f4b715533dfea545365eaf66de0574c5e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106784"
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Kurz: Připojení k Azure IoT Suite vzdálené monitorování předkonfigurované řešení aplikace logiky
[Microsoft Azure IoT Suite] [ lnk-internetofthings] předkonfigurovaného řešení vzdáleného monitorování je skvělý způsob, jak rychle začít s sadou začátku do konce funkce, která exemplifies řešení IoT. Tento kurz vás provede přidání aplikace logiky do vaší aplikace Microsoft Azure IoT Suite předkonfigurovanému řešení vzdáleného monitorování. Tyto kroky ukazují, jak může trvat i další řešení IoT díky připojení k obchodních procesů.

*Pokud hledáte návod o tom, jak zřídit předkonfigurované řešení vzdáleného monitorování, přečtěte si téma [kurz: Začínáme s předkonfigurovanými řešeními IoT][lnk-getstarted].*

Než začnete tento kurz, byste měli:

* Zřízení předkonfigurovaného řešení vzdáleného monitorování ve vašem předplatném Azure.
* Vytvoření účtu SendGrid umožňuje odesílání e-mailu, který aktivuje obchodních procesů. Si můžete zaregistrovat Bezplatný zkušební účet za [SendGrid](https://sendgrid.com/) kliknutím **bezplatné vyzkoušení**. Po registraci pro váš účet bezplatné zkušební verze, je potřeba vytvořit [klíč rozhraní API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) v SendGrid, která uděluje oprávnění k odesílání e-mailu. Později v tomto kurzu potřebujete tento klíč rozhraní API.

K dokončení tohoto kurzu, budete potřebovat Visual Studio 2015 nebo Visual Studio 2017 pro úpravu akce v předkonfigurovaném řešení back-endu.

Za předpokladu, že jste zřídili vzdáleného monitorování předkonfigurované řešení, přejděte do skupiny prostředků pro toto řešení [webu Azure portal][lnk-azureportal]. Skupina prostředků má stejný název jako název řešení jste zvolili, když jste zřídili řešení vzdáleného monitorování. Ve skupině prostředků uvidíte všechny zřízené prostředky Azure pro vaše řešení. Následující snímek obrazovky ukazuje příklad **skupiny prostředků** okno pro vzdálené monitorování předkonfigurovaného řešení:

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

Pokud chcete začít, nastavte pro použití s předkonfigurovaným řešením aplikace logiky.

## <a name="set-up-the-logic-app"></a>Nastavení aplikace logiky
1. Klikněte na tlačítko **přidat** v horní části okno skupiny prostředků na webu Azure Portal.
2. Vyhledejte **aplikace logiky**, vyberte ho a pak klikněte na tlačítko **vytvořit**.
3. Vyplňte **název** a použít stejné **předplatné** a **skupiny prostředků** , který jste použili při zřizování řešení vzdáleného monitorování. Klikněte na možnost **Vytvořit**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. Po dokončení nasazení uvidíte, že aplikace logiky je uveden jako prostředek ve vaší skupině prostředků.
5. Klikněte na aplikaci logiky, přejděte do okna aplikace logiky, vyberte **prázdná aplikace logiky** otevřete šablonu **návrhář pro Logic Apps**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. Vyberte **požádat o**. Tuto akci Určuje, že příchozí žádost protokolu HTTP s konkrétní JSON naformátovaný datovou část funguje jako trigger.
7. Vložte následující kód do schématu JSON těla požadavku:
   
    ```json
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > Po uložení aplikace logiky, ale nejprve je nutné přidat akci, můžete zkopírovat adresu URL pro požadavek HTTP post.
   > 
   > 
8. Klikněte na tlačítko **+ nový krok** v části Ruční aktivační události. Pak klikněte na tlačítko **přidat akci**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. Vyhledejte **SendGrid – odeslání e-mailu** a klikněte na něj.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. Zadejte název připojení, jako například **SendGridConnection**, zadejte **klíč rozhraní API SendGrid** jste vytvořili při nastavování účtu SendGrid a klikněte na tlačítko **vytvořit**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. Přidat e-mailové adresy, které vlastníte i **z** a **k** pole. Přidat **vzdálené sledování upozornění [DeviceId]** k **subjektu** pole. V **e-mailu** pole, přidat **zařízení [DeviceId] ohlásil [measurementName] s hodnotou [measuredValue].** Můžete přidat **[DeviceId]**, **[measurementName]**, a **[measuredValue]** kliknutím **vložíte data z předchozích kroků** oddíl.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. Klikněte na tlačítko **Uložit** v horní nabídce.
13. Klikněte na tlačítko **požádat o** aktivační události a zkopírujte **Http Post na tuto adresu URL** hodnotu. Tuto adresu URL budete potřebovat později v tomto kurzu.

> [!NOTE]
> Logic Apps umožňují spouštět [mnoho různých typů akce] [ lnk-logic-apps-actions] včetně akcí v Office 365. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>Nastavit EventProcessor webové úlohy
V této části připojit vaše předkonfigurované řešení na aplikaci logiky, který jste vytvořili. Tento úkol provést, přidejte adresu URL pro spuštění aplikace logiky pro akci, která je vyvoláno, když hodnota snímače zařízení překračuje prahovou hodnotu.

1. Klonovat nejnovější verzi pomocí klienta git [azure-iot-remote-monitoring úložiště github][lnk-rmgithub]. Příklad:
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. V sadě Visual Studio, otevřete **RemoteMonitoring.sln** z místní kopie úložiště.
3. Otevřít **ActionRepository.cs** soubor **infrastruktury\\úložiště** složky.
4. Aktualizace **actionIds** slovník s **Http Post na tuto adresu URL** jste si poznamenali z aplikace logiky následujícím způsobem:
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. Uložte změny v řešení a ukončení sady Visual Studio.

## <a name="deploy-from-the-command-line"></a>Nasazení z příkazového řádku
V této části nasadíte vaše aktualizovanou verzi řešení vzdáleného monitorování nahradit verzi aktuálně spuštěné v Azure.

1. Následující [dev nastavení] [ lnk-devsetup] pokynů pro nastavení prostředí pro nasazení.
2. Pokud chcete nasadit místně, postupujte [místní nasazení] [ lnk-localdeploy] pokyny.
3. Nasadit do cloudu a aktualizovat vaše stávající nasazení cloud, postupujte [cloudové nasazení] [ lnk-clouddeploy] pokyny. Název původního nasazení použijte jako název nasazení. Například pokud byla volána v původním nasazení z **demologicapp**, použijte následující příkaz:
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   Po spuštění skriptu sestavení, nezapomeňte použít stejný účet Azure, předplatné, oblasti a instance Active Directory, který jste použili při zřizování řešení.

## <a name="see-your-logic-app-in-action"></a>Aplikace logiky v akci
Předkonfigurované řešení vzdáleného monitorování má dvě pravidla ve výchozím nastavení při zřizování řešení. Obě pravidla jsou na **SampleDevice001** zařízení:

* Teplota > 38.00
* Vlhkosti > 48.00

Aktivačních událostí teplotní pravidlo **vyvolat upozornění** akce a vlhkost pravidlo aktivační události **SendMessage** akce. Za předpokladu, že používá stejnou adresu URL pro obě akce **ActionRepository** třídy, vaše triggery aplikace logiky pro buď pravidlo. Obě pravidla SendGrid použít k odesílání e-mailu **k** adresu s podrobnostmi o upozornění.

> [!NOTE]
> Aplikace logiky bude nadále aktivuje pokaždé, když je splněna prahovou hodnotu. Aby se zabránilo zbytečným e-mailů, můžete zakázat pravidlech portálu řešení nebo v aplikaci logiky zakázat [webu Azure portal][lnk-azureportal].
> 
> 

Kromě doručování e-mailů, můžete také zobrazit při spuštění aplikace logiky na portálu:

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Další postup
Teď, když aplikace logiky jste použili pro připojení předkonfigurovaného řešení obchodních procesů, které se další informace o možností pro přizpůsobení předkonfigurovaných řešení:

* [Použití dynamické telemetrie s předkonfigurovaného řešení vzdáleného monitorování][lnk-dynamic]
* [Informace metadat zařízení v předkonfigurovaném řešení vzdáleného monitorování][lnk-devinfo]

[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
