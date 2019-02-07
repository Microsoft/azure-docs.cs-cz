---
title: Připojení zařízení v Azure IoT Central | Dokumentace Microsoftu
description: Tento článek představuje klíčové koncepty týkající se připojení zařízení v Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 5d90980172b49071601edeebbf28907def862e4e
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824011"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Připojení zařízení v Azure IoT Central

Tento článek představuje klíčové koncepty týkající se připojení zařízení v Microsoft Azure IoT Central.

Azure IoT Central používá [Azure IoT Hub Device Provisioning service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps), povolování IoT Central pro podporu registrace a připojování zařízení ve velkém měřítku.

-   Zákazníci teď můžete vygenerovat přihlašovací údaje zařízení a nakonfigurovat zařízení offline bez nutnosti nejprve zaregistrovat zařízení v IoT Central
-   IoT Central podporuje připojení zařízení se doporučuje X509 cert založené na připojení, zatímco i nadále bude podporovat a vylepší připojení sdílených přístupových podpisů (SAS)
-   IoT Central zákazníci můžou teď začít přenášet své vlastní ID zařízení k registraci zařízení v IoT Central umožňuje snadnou integraci s existujícími systémy administrativní
-   Existuje jeden konzistentní způsob připojení zařízení k IoT Central 

>[!NOTE]
>IoT Central používá službu Azure IoT Device Provisioning (DPS) pod pro všechny registrace zařízení a připojení, [Další informace o distribučních bodů](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

Podle vašeho použití případu postupujte podle pokynů pro připojení zařízení k IoT Central
1. [Rychlé připojení jedno zařízení (použití sdílených přístupových podpisů)](#connect-a-single-device)
1. [Připojení zařízení ve velkém měřítku pomocí sdílených přístupových podpisů (SAS)](#connect-devices-at-scale-using-shared-access-signatures)
1. [Připojení zařízení ve velkém měřítku pomocí X509 certifikáty](#connect-devices-using-x509-certificates) **doporučuje pro produkční úlohy**
1. [Připojit bez první registrace zařízení](#connect-without-first-registering-devices) 


>[!NOTE]
>Tady je globální koncový bod zařízení, připojení a zřízení **global.azure. zařízení provisioning.net**.

## <a name="connect-a-single-device"></a>Připojení jedno zařízení
Pomocí SAS připojení jedno zařízení k IoT Central je jednoduché a zabere jenom pár kroků 
1. Přidat **skutečné zařízení** z Device Explorer, klikněte na **+ nový > skutečné** skutečné zařízení přidat.
    * Zadejte Id zařízení **<span style="color:Red">(by měl být malými písmeny)</span>** nebo použijte navrhovaný ID zařízení.
    * Zadejte název zařízení nebo použijte navrhovaný název   
    ![Přidání zařízení](media/concepts-connectivity/add-device.png)
1. Získat podrobnosti o připojení, jako **ID oboru, ID zařízení a primární klíč** pro přidání zařízení kliknutím **připojit** na stránce zařízení.
    * **[ID rozsahu](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)**  za aplikace IoT Central je a generuje distribučních bodů, zajišťuje jedinečné ID zařízení v rámci aplikace.
    * **Id zařízení** je jedinečné ID na aplikaci, zařízení je potřeba poslat Id zařízení jako součást volání registrace zařízení.   
    * **Primární klíč** je SAS token vygenerovaný IoT Central pro toto konkrétní zařízení. 
    ![Podrobné informace o připojení](media/concepts-connectivity/device-connect.png)
1. Tyto podrobnosti připojení **Identity zařízení, název zařízení a primární klíč zařízení** ve svém kódu zařízení pro zřízení a připojení zařízení a začnou zobrazovat data protékají okamžitě. Pokud používáte zařízení použijte MxChip, [podrobných pokynů](howto-connect-devkit-experimental.md#add-a-real-device), spusťte z části **připravit zařízení DevKit**.   

    V následující tabulce jsou odkazy na další jazyky, které chcete použít.

    *   **Jazyk C:** Pokud používáte C, postupujte podle [tohoto klienta zařízení ukázka C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) připojit zařízení vzorku. Použijte následující nastavení ve vzorku.   

         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```

    *   **Node.js:**  Pokud chcete pomocí Node.js [pomocí podrobných pokynů zde](tutorial-add-device-experimental.md#prepare-the-client-code), spusťte z části **Příprava klientský kód**.



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>Připojení zařízení ve velkém měřítku pomocí sdílených přístupových podpisů

Připojení zařízení ve velkém měřítku pomocí SAS IoT Central, jsou dva kroky zahrnuté 
1. **Registrace zařízení** podle jejich importování do IoT Central prostřednictvím sdíleného svazku clusteru souboru a exportovat zařízení se podrobnosti o připojení zařízení používat pro připojení vašich zařízení
1. **Nastavení zařízení** zařízení je naprogramovat se podrobnosti o připojení ( **ID oboru, ID zařízení a primární klíč**), aby mohla volat službu zřizování můžete získat jeho připojení info/IoT přiřazení centrální aplikace je zapnuté.

>[!NOTE]
>Upřesňující možnost je k dispozici také v případě zařízení se můžete připojit bez nutnosti nejprve zaregistrovat zařízení v IoT Central [Další informace najdete tady](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

**Registrace zařízení**

K připojení velkého počtu zařízení pro vaši aplikaci, Azure IoT Central nabídky hromadně importovat zařízení prostřednictvím souboru CSV. 

Požadavky na soubor CSV: Soubor CSV by měl obsahovat následující sloupce (a hlavičky)
1.  IOTC_DeviceID  **<span style="color:Red">(by měl být malými písmeny)</span>**
1.  IOTC_DeviceName (volitelné)



Importovat zařízení k registraci ve vaší aplikaci
1.  Zvolte **Explorer** v levé navigační nabídce.
1.  Na levém panelu vyberte šablonu zařízení, pro které chcete hromadně vytvořit zařízení. 
1.  Klikněte na tlačítko **Import**, vyberte soubor CSV obsahující seznam ID zařízení k importu.
Soubor CSV by měl obsahovat následující sloupce (a hlavičky)
    *   IOTC_DeviceID  **<span style="color:Red">(by měl být malými písmeny)</span>**
    *   IOTC_DeviceName (volitelné)
1.  Po dokončení importu zprávu o úspěšném dokončení se zobrazí v mřížce zařízení.

Export zařízení načte podrobné informace o připojení Export vytvoří soubor CSV s Id zařízení, název zařízení a klíč zařízení. Tyhle podrobnosti použijte pro připojení zařízení k IoT Central.
Chcete-li hromadně exportovat zařízení z vaší aplikace:
1.  Zvolte **Explorer** v levé navigační nabídce.
1.  Vyberte zařízení, která chcete exportovat a pak klikněte na tlačítko **exportovat** akce.
1.  Po dokončení exportu zprávu o úspěšném dokončení se zobrazí spolu s odkazem na stažení generovaného souboru.
1.  Klikněte na zprávy o úspěchu a stáhněte soubor do místní složky na disku.
1.  Exportovaný soubor CSV bude obsahovat následující sloupce informace: **Id zařízení, název zařízení, zařízení primárního/sekundárního klíče a primárního a sekundárního kryptografické otisky certifikátů**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


**Nastavení zařízení**

Tyto podrobnosti připojení **Identity zařízení (IOTC_DEVICEID), primární klíč zařízení (IOTC_SASKEY_PRIMARY) a ID oboru** ve vašem kódu zařízení ke zřízení a připojení zařízení. Pokud nemáte, získejte **Id oboru** z vaší aplikace IoT Central **správy > připojení zařízení > ID oboru**.
Pokud používáte **MxChip** zařízení pro připojení postupujte podle [podrobných pokynů](howto-connect-devkit-experimental.md#add-a-real-device), spusťte z části **připravit zařízení DevKit**.   

V následující tabulce jsou odkazy na další jazyky, které chcete použít.

   *   **Jazyk C:** Pokud používáte C postupujte [tohoto klienta zařízení ukázka C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) připojit zařízení vzorku. Použijte následující nastavení ve vzorku.   
         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```
    * **Node.js:**  Pokud chcete pomocí Node.js [pomocí podrobných pokynů zde](tutorial-add-device-experimental.md#prepare-the-client-code), spusťte z části **Příprava klientský kód**.


## <a name="connect-devices-using-x509-certificates"></a>Připojení zařízení pomocí X509 certifikáty

Pomocí X.509 certifikáty jako mechanismus ověřování je vynikající způsob, jak škálovat **produkční** a zjednodušuje zřizování zařízení. Certifikáty X.509 jsou obvykle uspořádány v řetězu certifikátů, ve kterém je každý certifikát v řetězu podepsány privátní klíč další vyšší certifikátu a tak dále, ukončuje v certifikát podepsaný svým držitelem kořenové důvěryhodnosti. Tím dojde k vytvoření delegovaného řetěz certifikátů z kořenového certifikátu vygenerovaného důvěryhodné kořenové certifikační autority (CA) dolů prostřednictvím každý zprostředkující certifikační Autority pro certifikát koncové entity "typu list", nainstalované v zařízení. Další informace najdete v tématu [zařízení ověřování pomocí certifikátů X.509 certifikační Autority](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Připojení zařízení k IoT Central X509 pomocí certifikátů, existuje se využívá řada tři klíčové kroky: 
1. **Konfigurace nastavení připojení** v IoT Central aplikaci tak, že přidání/ověříte X509 kořenový nebo zprostředkující certifikát použít k vytvoření certifikátů zařízení.  Existují dva kroky pro konfiguraci nastavení připojení pro X509 certifikáty.  

    *   **Přidat X509 kořenový nebo zprostředkující certifikát** použijete k vytvoření certifikátů zařízení typu list. Přejděte na Správa > připojení zařízení > certifikáty. 
    
        ![Nastavení připojení](media/concepts-connectivity/connection-settings.png)
    *   **Ověření certifikátů:** Ověření vlastnictví certifikátu zajišťuje, že uživatele nahrávajícího certifikátu ve vlastnictví privátní klíč certifikátu. Chcete-li ověřit certifikát
        *  Vygenerovat ověřovací kód, klikněte na tlačítko vedle pole ověřovací kód a vygenerovat ověřovací kód. 
        *  Vytvořte certifikát pro ověření X.509 s ověřovacím kódem, uložte certifikát jako soubor .cer. 
        *  Nahrát podepsaný ověřovacího certifikátu a klikněte na tlačítko ověřit.

        ![Nastavení připojení](media/concepts-connectivity/verify-cert.png)
    *   **Sekundární certifikát:** Během životního cyklu řešení IoT musíte vrátit certifikáty. Dva hlavní důvody pro certifikáty se zajištěním provozu by bezpečnosti a vypršení platnosti certifikátů. Sekundární certifikáty se používají k zkrátit výpadky pro zařízení s pokusem o zřízení, když aktualizujete primární certifikát.

    **TESTOVÁNÍ POUZE PRO ÚČELY** 
    
    Níže jsou některé nástroje příkazového řádku nástroje, které můžete použít ke generování certifikátů certifikační Autority a certifikátů zařízení.

    * Pokud používáte MxChip tady je [nástroj příkazového řádku](https://aka.ms/iotcentral-docs-dicetool) ke generování certifikační Autority ho přidejte do své aplikace IoT Central a ověřit certifikáty. 

    *   Použijte tento [nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) do
        * Vytvořte řetěz certifikátů (v Githubu dokumentace postupujte podle kroku 2). 
         Uložit jako soubory .cer certifikáty a nahrajte do IoT Central (primární).   
        * Získat ověřovací kód z aplikace IoT Central, certifikát (v Githubu dokumentace postupujte podle kroku 3) můžete vytvořit a odeslat k ověření. 
        * Vytvoření certifikátů listu s vaším zařízením Id jako parametr pro nástroj (postupujte podle kroku 4). Uložte certifikát a použijte ji na svém zařízení.     

1. **Registrace zařízení** podle jejich importování do IoT Central prostřednictvím souboru CSV.

1. **Nastavení zařízení** : Generovat listové certifikáty pomocí nahrané kořenový certifikát. Ujistěte se, že používáte **ID zařízení** jako záznam CNAME v listu certifikáty a je v **malými písmeny**. Tady je [nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) ke generování certifikátů listu nebo zařízení pro **pouze testování účely**.

    Program zařízení umožňuje získat jeho podrobnosti připojení a IoT Central přiřazení aplikace při přepnutí informace o službě zřizování.    

    **Odkaz na další** 
    *   Ukázková implementace pro [raspberrypi nebo.](https://aka.ms/iotcentral-docs-Raspi-releases)  

    *   [Ukázky klienta zařízení v jazyce C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

>[!NOTE]
>Použití **ID zařízení** jako záznam cname generovat listové certifikáty pro zařízení.

>[!NOTE]
>**ID zařízení** by měl být malými písmeny 
 
## <a name="connect-without-first-registering-devices"></a>Připojit bez první registrace zařízení
Jeden z klíčových scénářů, které umožňuje IoT Central je pro výrobce OEM, do zařízení hromadné výroby, vytvoření přihlašovacích údajů a nakonfigurujete je v objektu pro vytváření bez nutnosti nejprve zaregistrujte je v IoT Central. Jakmile se zařízení jsou zapnuté a připojte se k IoT Central operátor schválí zařízení pro připojení k aplikaci IoT Central.

Níže se tok připojil zařízení s touto funkcí

![Nastavení připojení](media/concepts-connectivity/device-connection-flow.png)


Postupujte podle pokynů na základě vaší volby schéma ověřování zařízení (X509/SAS)

1. **nastavení připojení** 
    * **X509 certifikáty:** [Přidat a ověřit kořenový nebo zprostředkující certifikát](#connect-devices-using-x509-certificates) a použít ho ke generování certifikátů zařízení v dalším kroku.
    * **SAS:** Zkopírujte primární klíč (klíč SAS pro skupinu pro tuto aplikaci IoT Central je tento klíč) a použít ho ke generování klíče SAS zařízení v dalším kroku. 
![Nastavení připojení SAS](media/concepts-connectivity/connection-settings-sas.png)

1. **Vytvoření přihlašovacích údajů pro zařízení** 
    *   **Certifikáty X509:** Generovat listové certifikáty pro zařízení s použitím kořenové nebo zprostředkující certifikát, který jste přidali do této aplikace. Ujistěte se, že používáte **ID zařízení** jako záznam cname v listu certifikáty a  **<span style="color:Red">(by měl být malými písmeny)</span>**. Tady je [nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) ke generování certifikátů listu nebo zařízení pro testování.
    *   **SAS** klíče zařízení SAS můžete vygenerovat pomocí to [nástroj příkazového řádku](https://www.npmjs.com/package/dps-keygen). Použijte primární SAS klíč (klíč SAS pro skupiny) z předchozího kroku. Ujistěte se, že ID zařízení  **<span style="color:Red">je malými písmeny</span>**.

        Použití následujících pokynů k vygenerování klíče SAS zařízení           

        ```
        npm i -g dps-keygen
        ```
    
        **Použití**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Nastavení zařízení** 
    
     Flash zařízení s **ID oboru, ID zařízení, zařízení certifikát a SAS klíč** a zapněte zařízení pro připojení k aplikaci IoT Central.

1. **Připojení zařízení k IoT Central:** Po zapnutí zařízení připojit k Device Provisioning a IoT Central pro registraci.

1. **Přidružení zařízení do šablony:** Připojené zařízení se zobrazí v části **nepřidružené zařízení** v **Device Explorer**. Zařízení, Stav zřizování je **registrované**. **Přidružit** zařízení k šabloně příslušné zařízení a schválit zařízení pro připojení k aplikaci IoT Central. Zařízení získá podrobnosti o připojení pro aplikace IoT Central, a potom připojí a spustí odesílat data. Zřizování zařízení je teď dokončený a *Stav zřizování* se změní **zřízená**.

## <a name="device-provisioning-status"></a>Stav zřizování zařízení
Obsahuje sérii kroků při se využívá řada skutečné zařízení je připojené k Azure IoT Central 
1. **Registrovaný**: Zařízení je první **registrované**, což znamená vytvoření zařízení v IoT Central a má ID zařízení pro zařízení.
Registrace zařízení při  
    *   Jsou přidávány nové skutečné zařízení **Průzkumníka**
    *   Sadu zařízení přidána pomocí **Import** na **Průzkumníka**
    *   Zařízení, který nebyl registrován, ale nepřipojí k platné přihlašovací údaje a je viditelný v **zrušení přidružené** zařízení. 

    Ve všech případech *Stav zřizování* je **registrované**

1. **Zřízení**: Dalším krokem je, když se zařízení připojí pomocí platných přihlašovacích údajů IoT Central krok zajišťování dokončí (tak, že vytvoříte zařízení ve službě IoT Hub). Potom vrátí připojovací řetězec zařízení připojit a začít odesílat data. Zařízení *Stav zřizování* nyní se změní z **registrované** k **zřízená**.

1.  **Zablokuje**: Operátor může blokovat zařízení, jakmile je zařízení blokované nemůže odesílat data do IoT Central a bude nutné resetovat. Zařízení, které jsou blokovány mají *Stav zřizování* z **blokováno**. Operátor může také odblokování zařízení. Po odblokování zařízení *Stav zřizování* zpět jeho předchozí *Stav zřizování* (zaregistrované nebo zřízené). 

## <a name="getting-device-connection-string"></a>Získat připojovací řetězec zařízení
Můžete získat připojovací řetězec zařízení Iot hub pro službu Azure IoT Hub pomocí následujících kroků 
1. Získat podrobnosti o připojení, jako **ID oboru, ID zařízení, zařízení primární klíč** ze stránky zařízení (je teď na stránce zařízení > klikněte na tlačítko Připojit) 

    ![Podrobnosti připojení](media/concepts-connectivity/device-connect.png)

1. Získá připojovací řetězec zařízení pomocí nástroje příkazového řádku.
    Použití následujících pokynů k získání připojovacího řetězce zařízení  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **Použití**

    Chcete-li vytvořit připojovací řetězec, najít v části bin binárního souboru nebo složky
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    Další informace o [zde o nástroj keygen distribučních bodů.](https://www.npmjs.com/package/dps-keygen)

## <a name="sdk-support"></a>Podpora v sadě SDK

Nabídka sady SDK pro zařízení Azure nejjednodušší můžete implementovat kód na zařízení, která se připojuje k vaší aplikaci Azure IoT Central. Sady SDK pro následující zařízení jsou k dispozici:

- [Sada Azure IoT SDK pro jazyk C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK pro Python](https://github.com/azure/azure-iot-sdk-python)
- [Sada Azure IoT SDK pro Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Sada Azure IoT SDK pro Javu](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK pro .NET](https://github.com/azure/azure-iot-sdk-csharp)

Každé zařízení připojí pomocí jedinečných připojovací řetězec identifikující zařízení. Zařízení lze připojit pouze ke službě IoT hub, ve kterém je zaregistrovaný. Při vytváření skutečných zařízení v aplikaci Azure IoT Central aplikace vygeneruje připojovací řetězec k použití.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Funkce sady SDK a připojení k službě IoT Hub

Veškerá komunikace zařízení pomocí služby IoT Hub používá následující možnosti připojení k službě IoT Hub:

- [Zasílání zpráv typu zařízení cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Dvojčata zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

Následující tabulka shrnuje, jak Azure IoT Central zařízení funkce mapují k funkcích ve službě IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Měření: Telemetrická data | Zasílání zpráv typu zařízení cloud |
| Vlastnosti zařízení | Ohlášené vlastnosti dvojčete zařízení |
| Nastavení | Dvojče zařízení požadovaného a ohlášené vlastnosti |

Další informace o použití sady SDK pro zařízení, najdete v jednom z následujících článků příklad kódu:

- [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs-experimental.md)
- [Připojte Raspberry Pi zařízení do aplikace Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Připojte zařízení za kit DevDiv do aplikace Azure IoT Central](howto-connect-devkit-experimental.md).


## <a name="protocols"></a>Protokoly

Sady SDK pro zařízení se podporují následující síťové protokoly pro připojení do služby IoT hub:

- MQTT
- AMQP
- HTTPS

Informace o těchto protokolů rozdíl a pokyny o volbě jeden najdete v tématu [volba komunikačního protokolu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Pokud vaše zařízení nemohou použít žádný z podporovaných protokolů, můžete použít Azure IoT Edge na protokol převodu. IoT Edge podporuje další scénáře intelligence na the-edge pro snižování zátěže zpracování do hraničních zařízení z aplikace v Azure IoT Central.

## <a name="security"></a>Zabezpečení

Všechna data se vyměňují mezi zařízeními a vaše Azure IoT Central je zašifrovaná. IoT Hub provádí ověřování každý požadavek ze zařízení, která se připojuje k některé z koncových bodů určených pro zařízení služby IoT Hub. Zařízení se pokud chcete vyhnout, výměnu přihlašovacích údajů při přenosu, používá k ověření podepsané tokeny. Další informace najdete v tématu, [řízení přístupu ke službě IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> V současné době zařízení, která připojení k Azure IoT Central, musíte použít tokeny SAS. Certifikáty X.509 nejsou podporovány pro zařízení, která se připojují k Azure IoT Central.

## <a name="next-steps"></a>Další postup

Teď, když jste se dozvěděli o připojení zařízení v Azure IoT Central, tady jsou další navrhované kroky:

- [Příprava a připojte zařízení za DevKit](howto-connect-devkit-experimental.md)
- [Příprava a připojení Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs-experimental.md)
