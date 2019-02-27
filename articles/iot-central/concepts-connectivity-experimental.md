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
ms.openlocfilehash: edffc6677609537d8a07aeae45a57c5e88449099
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882650"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Připojení zařízení v Azure IoT Central

Tento článek představuje klíčových konceptů, které se vztahují na připojení zařízení v Microsoft Azure IoT Central.

Azure IoT Central používá [Azure IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) k registraci a připojení zařízení ve velkém měřítku.

Pomocí služby Device Provisioning Service:

- Zákazníci teď můžete vygenerovat přihlašovací údaje zařízení a nakonfigurovat zařízení offline bez nutnosti nejprve zaregistrovat zařízení v Azure IoT Central.
- Azure IoT Central podporuje připojení zařízení pomocí certifikátů X.509, zatímco i nadále bude podporovat a vylepší připojení pomocí signatur sdíleného přístupu.
- Azure IoT Central zákazníci teď můžou využívat vlastní ID zařízení k registraci zařízení v Azure IoT Central, což umožňuje snadnou integraci s existujícími systémy administrativní.
- Neexistuje jeden konzistentní způsob připojení zařízení k Azure IoT Central.

>[!NOTE]
>Azure IoT Central IoT Hub Device Provisioning Service používá pro všechny registrace zařízení a připojení. [Další informace](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

Podle vašemu případu použití, postupujte podle příslušných pokynů a připojení zařízení k Azure IoT Central:

- [Připojení jedno zařízení rychle (s použitím sdílených přístupových podpisů)](#connect-a-single-device)
- [Připojení zařízení ve velkém měřítku pomocí signatur sdíleného přístupu](#connect-devices-at-scale-using-shared-access-signatures)
- [Připojení zařízení ve velkém měřítku pomocí certifikátů X.509](#connect-devices-using-x509-certificates) (doporučeno pro produkční úlohy)
- [Připojit bez první registrace zařízení](#connect-without-first-registering-devices) 

>[!NOTE]
>Globální koncový bod pro zařízení se připojte a zřídit je **global.azure. zařízení provisioning.net**.

## <a name="connect-a-single-device"></a>Připojení jedno zařízení

Připojení jedno zařízení k Azure IoT Central podle použití sdílených přístupových podpisů:

1. Přidat **skutečné zařízení** z **Device Explorer**. Vyberte **+ nová** > **skutečné** skutečné zařízení přidat.
    - Zadejte **ID zařízení** (by měl být malými písmeny) nebo použijte ID zařízení navrhované.
    - Zadejte **název zařízení** nebo použijte navrhovaný název.

    ![Přidání zařízení](media/concepts-connectivity/add-device.png)

1. Získání podrobností o připojení, jako je **ID oboru**, **ID zařízení**, a **primární klíč**, pro přidání zařízení tak, že vyberete **připojit** na stránka zařízení.

    - **[ID rozsahu](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)**  je za aplikaci Azure IoT Central a vygeneruje se služba Device Provisioning. Tento Identifikátor slouží k zajištění jedinečné ID zařízení v rámci aplikace.
    - **ID zařízení** je jedinečné ID zařízení na aplikaci. Zařízení je potřeba poslat ID zařízení jako součást volání registrace.
    - **Primární klíč** token sdíleného přístupového podpisu je generováno pomocí Azure IoT Central pro toto konkrétní zařízení.
 
    ![Podrobnosti připojení](media/concepts-connectivity/device-connect.png)

1. Podrobnosti připojení **identitu zařízení**, **název zařízení**a zařízení **primární klíč** ve svém kódu zařízení pro zřízení a připojení zařízení a začnou zobrazovat tok dat prostřednictvím okamžitě. Pokud používáte zařízení MXChip IoT DevKit (DevKit), postupujte podle [podrobných pokynů](howto-connect-devkit-experimental.md#add-a-real-device)začíná v části "Příprava zařízení DevKit."

    Tady jsou odkazy na další jazyky, které chcete použít.

    - **Jazyk C:** Postupujte podle [tohoto klienta zařízení ukázka C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) připojit zařízení vzorku. Ve vzorku, použijte následující nastavení:

         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```

    - **Node.js:**  Postupujte podle [podrobných pokynů](tutorial-add-device-experimental.md#prepare-the-client-code)začíná v části "Příprava kód klienta."

## <a name="connect-devices-at-scale-by-using-shared-access-signatures"></a>Připojení zařízení ve velkém měřítku pomocí signatur sdíleného přístupu

Připojení zařízení ve velkém měřítku pomocí Azure IoT Central pomocí sdílené přístupové podpisy, jsou dvou krocích:

1. **Registrace zařízení:** Zaregistrujte zařízení podle jejich importování do Azure IoT Central prostřednictvím souboru CSV. Potom použijte **exportovat** akce k exportu vašeho zařízení a získat tak podrobné údaje jejich připojení zařízení.
1. **Nastavení zařízení:** Program zařízení s podrobnostmi o připojení (**ID oboru**, **ID zařízení**, a **primární klíč**). Když je zapnuté každé zařízení, bude volat služby Device Provisioning můžete získat jeho informace o připojení nebo Azure IoT Central přiřazení aplikace.

>[!NOTE]
>Upřesňující možnost je také k dispozici ve kterém můžete zařízení můžete připojit bez nutnosti nejprve zaregistrovat zařízení v Azure IoT Central. [Další informace](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

### <a name="device-registration"></a>Registrace zařízení

K připojení velkého počtu zařízení pro vaši aplikaci, Azure IoT Central podporuje hromadný import zařízení prostřednictvím souboru CSV. 

1. Importovat zařízení k registraci ve vaší aplikaci:

   1. Vyberte **Device Explorer** v nabídce vlevo.
   1. Na levém panelu, vyberte šablonu zařízení pro které chcete hromadně vytvořit zařízení. 
   1. Vyberte **Import**a potom vyberte soubor CSV obsahující seznam ID zařízení k importu. Soubor CSV by měl obsahovat následující sloupce (a záhlaví):

       - IOTC_DeviceID (by měl obsahovat malá písmena)
       - IOTC_DeviceName (volitelné)

   1. Po dokončení importu zprávu o úspěšném dokončení se zobrazí v mřížce zařízení.

1. Exportujte zařízení, která jejich podrobnosti připojení:

   **Exportovat** vytvoří soubor CSV s ID zařízení, název zařízení a klíče zařízení. Tyhle podrobnosti použijte pro připojení zařízení k Azure IoT Central. Pro export hromadné zařízení z vaší aplikace:

   1. Vyberte **Device Explorer** v nabídce vlevo.
   1. Vyberte zařízení, která chcete exportovat a pak vyberte **exportovat** akce.
   1. Po dokončení exportu zprávu o úspěšném dokončení se zobrazí spolu s odkazem na stažení generovaného souboru.
   1. Úspěch na zprávu klikněte a stáhněte soubor do místní složky na disku.
   1. Exportovaný soubor CSV bude obsahovat následující sloupce, které obsahují podrobnosti o připojení **ID zařízení**, **název zařízení**, **zařízení primární nebo sekundární klíč**a  **Primární nebo sekundární kryptografické otisky certifikátů**:

       - IOTC_DEVICEID
       - IOTC_DEVICENAME
       - IOTC_SASKEY_PRIMARY
       - IOTC_SASKEY_SECONDARY
       - IOTC_X509THUMBPRINT_PRIMARY
       - IOTC_X509THUMBPRINT_SECONDARY

### <a name="device-setup"></a>Instalace zařízení

 Chcete-li zřídit a připojení zařízení, použijte podrobné informace o připojení **Identity zařízení (IOTC_DEVICEID)**, **zařízení primární klíč (IOTC_SASKEY_PRIMARY)**, a **ID oboru** v kód vašeho zařízení. Pokud jste tak již neučinili, získat **ID oboru** z aplikace Azure IoT Central tak, že vyberete **správu** > **připojení zařízení**  >  **ID rozsahu**.

Pokud se připojujete DevKit zařízení, postupujte podle [podrobných pokynů](howto-connect-devkit-experimental.md#add-a-real-device)začíná v části "Příprava zařízení DevKit."

Tady jsou odkazy na další jazyky, které chcete použít.

- **Jazyk C:** Postupujte podle [tohoto klienta zařízení ukázka C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) připojit zařízení vzorku. Ve vzorku, použijte následující nastavení:

     ```c
     hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

     ## Enter the Device Id and Symmetric keys 
     prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
    ```

- **Node.js:** Postupujte podle [podrobných pokynů](tutorial-add-device-experimental.md#prepare-the-client-code)začíná v části "Příprava kód klienta."

## <a name="connect-devices-by-using-x509-certificates"></a>Připojení zařízení pomocí certifikátů X.509

Jako mechanismus ověřování pomocí certifikátů X.509 je vynikající způsob, jak škálovat produkčního prostředí a zjednodušuje zřizování zařízení. Certifikáty X.509 jsou obvykle uspořádány v řetězu certifikátů vztahu důvěryhodnosti, ve kterém je každý certifikát v řetězu podepsány privátní klíč certifikátu další vyšší a tak dále, s koncovkou certifikát podepsaný svým držitelem. Tato struktura vytváří delegované řetěz certifikátů z kořenového certifikátu, který je generován důvěryhodné kořenové certifikační autority (CA) dolů prostřednictvím jednotlivých zprostředkující certifikační Autority pro certifikát koncové entity "typu list", který je nainstalovaný na zařízení. Další informace najdete v tématu [zařízení ověřování pomocí certifikátů webu X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Připojení zařízení k Azure IoT Central pomocí certifikátů X.509, existují tři klíčové kroky zahrnuté:
 
1. **Konfigurace nastavení připojení** v aplikaci Azure IoT Central přidáním nebo ověřování X.509 kořenový nebo zprostředkující certifikát, který se používá ke generování certifikátů zařízení. Existují tři kroky pro konfiguraci nastavení připojení pro certifikáty X.509:  

    - **Přidat X.509 kořenový nebo zprostředkující certifikát** , které používáte k vytvoření certifikátů zařízení typu list. Přejděte na **správu** > **připojení zařízení** > **certifikáty**. 
    
       ![Nastavení připojení](media/concepts-connectivity/connection-settings.png)

    - **Ověřte certifikát.** Ověření vlastnictví certifikátu se zajistí, že odeslání certifikát nemá privátní klíč certifikátu. Chcete-li ověřit certifikát:

        - Vygenerujte ověřovací kód. Vyberte tlačítko vedle **ověřovací kód** pole k vygenerování tohoto kódu. 
        - Vytvořte certifikát pro ověření X.509 s ověřovacím kódem. Uložte si certifikát jako soubor .cer. 
        - Nahrát podepsaný ověřovací certifikát a vyberte **ověřte**.

        ![Nastavení připojení](media/concepts-connectivity/verify-cert.png)

    - **Přidání sekundárního certifikátu.** Během životního cyklu řešení IoT musíte vrátit certifikáty. Dva hlavní důvody pro certifikáty se zajištěním provozu jsou porušením zabezpečení a vypršení platnosti certifikátů. Sekundární certifikáty snížit prostoje pro zařízení, které se pokoušejí o zřízení, když aktualizujete primární certifikát.

      **Pouze pro testovací účely:**
    
      Tady jsou některé nástroje příkazového řádku nástroje, které lze použít ke generování certifikátů certifikačních Autorit a certifikátů zařízení.

      - Pokud používáte zařízení DevKit, tady je [nástroj příkazového řádku](https://aka.ms/iotcentral-docs-dicetool) ke generování certifikátů certifikační Autority. Přidat do aplikace Azure IoT Central a ověřit certifikáty. 

      - Použití [tento nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) na:

          - Vytvořte řetěz certifikátů (krok použijte 2 v dokumentace Githubu). Uložit jako soubory .cer certifikáty a jejich nahrání do Azure IoT Central (jako primární certifikáty).
          - Získat ověřovací kód z aplikace Azure IoT Central, vygenerujte certifikát (krok použijte 3 v dokumentace Githubu) a nahrát certifikát, který chcete ověřit. 
          - Vytvoření certifikátů listu se svým ID zařízení jako parametr pro nástroj (postupujte podle kroku 4). Uložte si certifikát a použijte ji na svém zařízení.

1. **Registrace zařízení** podle jejich importování do Azure IoT Central prostřednictvím souboru CSV.

1. **Nastavení zařízení**: Generovat listové certifikáty pomocí nahrané kořenový certifikát. Ujistěte se, že používáte ID zařízení jako záznam CNAME v listu certifikáty a ujistěte se, že je na malá písmena. Tady je [nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) pro generování certifikátů listu nebo zařízení pro **testování mají jenom**.

1. **Program zařízení informace o službě zřizování**, aby mohla získat jeho podrobnosti připojení a jeho Azure IoT Central přiřazení aplikace, když je zapnuté.

    Další informace najdete v těchto článcích:

    - [Ukázková implementace pro Raspberry Pi](https://aka.ms/iotcentral-docs-Raspi-releases)  

    - [Ukázky klienta zařízení v jazyce C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-first-registering-devices"></a>Připojit bez první registrace zařízení

Jednou z klíčových scénářů, které podporuje Azure IoT Central je pro výrobce OEM velkokapacitních od výrobce zařízení, vytvoření přihlašovacích údajů a konfiguraci zařízení v objektu pro vytváření, aniž byste museli nejdřív zaregistrovat zařízení v Azure IoT Central. Poté, co zařízení jsou zapnuté a pokusí se připojit k Azure IoT Central, operátor schválí zařízení pro připojení k aplikaci Azure IoT Central.

Tady se tok připojil zařízení s touto funkcí:

![Nastavení připojení](media/concepts-connectivity/device-connection-flow.png)

Postupujte podle kroků na základě vaší volby schématu ověřování zařízení (certifikáty X.509 nebo sdílených přístupových podpisů):

1. **Konfigurace nebo získání nastavení připojení:**

    - **Certifikáty X.509:** [Přidání a ověření kořenový nebo zprostředkující certifikát](#connect-devices-using-x509-certificates) a použít ho ke generování certifikátů zařízení v dalším kroku.
    - **Sdílené přístupové podpisy:** Zkopírujte primární klíč (Tento klíč je klíč skupiny sdíleného přístupového podpisu pro tuto aplikaci Azure IoT Central) a použít ho ke generování klíče podpisu zařízení sdíleného přístupu v dalším kroku.

       ![Nastavení připojení pro sdílených přístupových podpisů](media/concepts-connectivity/connection-settings-sas.png)

1. **Vytvoření přihlašovacích údajů zařízení:**

    - **Certifikáty X.509:** Generovat listové certifikáty pro vaše zařízení s použitím kořenový nebo zprostředkující certifikát, které jste přidali k této aplikaci. Ujistěte se, že používáte ID zařízení jako záznam CNAME v listu certifikáty a ujistěte se, že je malá písmena. Tady je [nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) pro generování certifikátů listu nebo zařízení pro testování.
    - **Sdílené přístupové podpisy:** Ke generování zařízení sdíleného přístupu podpisové klíče, použijte toto [nástroj příkazového řádku](https://www.npmjs.com/package/dps-keygen). Použijte klíč primární sdílený přístupový podpis (klíč skupiny sdílený přístupový podpis) z předchozího kroku. Ujistěte se, že zařízení je ID malými písmeny.

        Chcete-li získat připojovací řetězec zařízení použijte následující příkaz: 

        ```
        npm i -g dps-keygen
        ```
    
        Ke generování klíče sdíleného přístupového podpisu zařízení použijte následující příkaz:
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Nastavení zařízení:** Flash každému zařízení **ID oboru**, **ID zařízení**, a **certifikátu nebo SAS klíč zařízení**a tato zařízení pro připojení k aplikaci Azure IoT Central.

1. **Připojení zařízení k Azure IoT Central:** Po zařízení jsou zapnuté, pro registraci se připojit k zařízení zřizování služby Azure IoT Central.

1. **Přidružení zařízení do šablony:** Připojená zařízení zobrazí v části **zrušení přidružené zařízení** v **Device Explorer**. Stav zřizování zařízení je **registrované**. **Přidružit** zařízení k šabloně příslušné zařízení a schválit zařízení pro připojení k aplikaci Azure IoT Central. Zařízení získat podrobnosti o připojení pro aplikaci Azure IoT Central, a potom připojit a začít odesílat data. Zřizování zařízení je nyní dokončena a **Stav zřizování** pro zařízení se změní **zřízená**.

## <a name="device-provisioning-status"></a>Stav zřizování zařízení

Pokud se skutečné zařízení připojené k Azure IoT Central, dojde k následujícím krokům:

1. **Registrovaný**: Nejprve registrace zařízení, což znamená vytvoření zařízení v Azure IoT Central a nemá ID zařízení pro zařízení. Je zařízení registrováno při:

    * Přidá nový skutečné zařízení **Device Explorer**
    * Přidání skupiny zařízení s použitím **Import** v **Device Explorer**
    * Zařízení, která nebyla registrována připojuje pomocí platných přihlašovacích údajů a je viditelný v **zrušení přidružená zařízení.**

    Ve všech výše uvedených případech **Stav zřizování** je **registrované**.

1. **Zřízení**: Když se zařízení připojí pomocí platných přihlašovacích údajů, Azure IoT Central se dokončí zřizování kroku (tak, že vytvoříte zařízení ve službě IoT Hub). Azure IoT Central pak vrátí připojovací řetězec do zařízení, takže se můžete připojit a začít odesílat data. Zařízení **Stav zřizování** se změní z **registrované** k **zřízená**.

1. **Zablokuje**: Operátor může blokovat zařízení. Po zařízením se zablokuje, kterou nelze odesílat data do Azure IoT Central a bude mít k obnovení. Zařízení, které jsou blokovány mají **Stav zřizování** z **blokováno**. Operátor může také odblokování zařízení. Po jeho má odblokováno, zařízení **Stav zřizování** vrátí do stavu předchozího (**registrované** nebo **zřízená**). 

## <a name="get-the-device-connection-string"></a>Získání připojovacího řetězec zařízení

Připojovací řetězec služby Iot Hub zařízení do služby Azure IoT Hub můžete získat pomocí následujících kroků:

1. Získat podrobnosti o připojení zařízení, jako **ID oboru**, **ID zařízení**, a **primární klíč** z **připojení zařízení** stránky. Tyto informace, přejděte k **zařízení** stránku a vybrat **připojit**. 

    ![Podrobnosti připojení](media/concepts-connectivity/device-connect.png)

1. Získání připojovacího řetězce zařízení pomocí nástroje příkazového řádku keygen distribučních bodů. Chcete-li získat připojovací řetězec zařízení použijte následující příkaz:  

    ```cmd/sh
    npm i -g dps-keygen
    ```

    Vytvoření připojovacího řetězce, vyhledejte binární soubor v rámci *bin /* složky:

    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```

    [Další informace o nástroj dps keygen](https://www.npmjs.com/package/dps-keygen).

## <a name="sdk-support"></a>Podpora v sadě SDK

Sady SDK Azure IoT nabízejí Nejsnadnějším způsobem, jak používat kód na zařízeních, která se připojují k vaší aplikaci Azure IoT Central. Následující sady SDK jsou k dispozici:

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

Následující tabulka shrnuje, jak Azure IoT Central funkcí na zařízeních se mapují na funkcích ve službě IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Měření: Telemetrická data | Zasílání zpráv typu zařízení cloud |
| Vlastnosti zařízení | Ohlášené vlastnosti dvojčete zařízení |
| Nastavení | Dvojče zařízení požadovaného a ohlášené vlastnosti |

Další informace o použití sady SDK Azure IoT, naleznete v následujících článcích pro ukázkový kód:

- [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs-experimental.md)
- [Připojte Raspberry Pi zařízení do aplikace Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Připojení MXChip IoT DevKit zařízení k vaší aplikaci Azure IoT Central](howto-connect-devkit-experimental.md)


## <a name="protocols"></a>Protokoly

Azure IoT SDK podporují následující síťové protokoly pro připojení do služby IoT hub:

- MQTT
- AMQP
- HTTPS

Informace o těchto protokolů a pokyny o volbě jeden najdete v tématu [volba komunikačního protokolu](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Pokud vaše zařízení nemohou použít žádný z podporovaných protokolů, můžete použít Azure IoT Edge na protokol převodu. IoT Edge podporuje další scénáře intelligence na the-edge pro snižování zátěže zpracování do hraničních zařízení z aplikace v Azure IoT Central.

## <a name="security"></a>Zabezpečení

Všechna data se vyměňují mezi zařízeními a aplikace Azure IoT Central je zašifrovaná. IoT Hub provádí ověřování každý požadavek ze zařízení, která se připojuje k některé z koncových bodů určených pro zařízení služby IoT Hub. Zařízení se pokud chcete vyhnout, výměnu přihlašovacích údajů při přenosu, používá k ověření podepsané tokeny. Další informace najdete v tématu [Řízení přístupu ke službě IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

## <a name="next-steps"></a>Další postup

- [Příprava a připojte zařízení za MXChip IoT DevKit](howto-connect-devkit-experimental.md)
- [Příprava a připojte zařízení za Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs-experimental.md)
