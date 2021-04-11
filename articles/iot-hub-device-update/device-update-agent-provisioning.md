---
title: Zřizování aktualizace zařízení pro Azure IoT Hub agenta | Microsoft Docs
description: Zřizování aktualizace zařízení pro agenta Azure IoT Hub
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: e778c7ee14d2115bf6d8cf7f12ceaa61e364a4a2
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120166"
---
# <a name="device-update-agent-provisioning"></a>Zřizování agenta aktualizace zařízení

Agent modulu aktualizace zařízení může běžet společně s jinými systémovými procesy a [IoT Edge moduly](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) , které se připojují k vašemu IoT Hub jako součást stejného logického zařízení. Tato část popisuje, jak zřídit agenta aktualizace zařízení jako identitu modulu. 


## <a name="module-identity-vs-device-identity"></a>Identita modulu vs – identita zařízení

V IoT Hub v rámci každé identity zařízení můžete vytvořit až 50 identit modulů. Každá identita modulu implicitně generuje nevlákenný modul. Na straně zařízení vám sady SDK pro IoT Hub zařízení umožňují vytvářet moduly, ve kterých každá z nich otevře nezávislé připojení k IoT Hub. Identitu modulu a modul se dodávají podobně jako identita zařízení a vyzdvojení zařízení, ale s jemnější členitosti. [Další informace o identitách modulů v IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Podpora pro aktualizaci zařízení

U aktualizace zařízení se aktuálně podporují následující typy zařízení IoT:

* Zařízení se systémem Linux (IoT Edge a zařízení bez IoT Edge):
    * Obrázek A/B aktualizace:
        - Yocto-ARM64 (referenční image) rozšiřitelná prostřednictvím open source k [vytváření vlastních imagí](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) pro jiné architektury podle potřeby.
        - Simulátor Ubuntu 18,04
       
    * Agent balíčku podporuje sestavení pro následující platformy a architektury:
        - Agent balíčku Ubuntu Server 18,04 x64 
        - Debian 9 
        
* Omezená zařízení:
    * Ukázky agenta aktualizace zařízení AzureRTOS: [aktualizace zařízení pro azure IoT Hub kurz pro Azure – reálný čas – operační systém](device-update-azure-real-time-operating-system.md)

* Odpojená zařízení: 
    * [Vysvětlení podpory pro nepřipojenou aktualizaci zařízení](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Požadavky  

Pokud nastavujete zařízení IoT/IoT Edge zařízení pro [aktualizace na základě balíčků](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts), přidejte do úložišť počítačů Packages.Microsoft.com pomocí následujících kroků:

1. Přihlaste se k počítači nebo zařízení IoT, na které máte v úmyslu nainstalovat agenta aktualizací zařízení.

1. Otevřete okno terminálu.

1. Nainstalujte konfiguraci úložiště, která odpovídá operačnímu systému vašeho zařízení.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Zkopírujte vygenerovaný seznam do adresáře sources. list. d.
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Nainstalujte veřejný klíč Microsoft GPG.
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Postup zřízení agenta aktualizace zařízení jako identity modulu

Tato část popisuje, jak zřídit agenta aktualizace zařízení jako identitu modulu v IoT Edge povolených zařízeních, zařízeních bez hraničních IoT a dalších zařízeních IoT.


### <a name="on-iot-edge-enabled-devices"></a>Na zařízeních s povoleným IoT Edge

Podle těchto pokynů můžete zřídit agenta aktualizace zařízení v [zařízeních s podporou IoT Edge](https://docs.microsoft.com/azure/iot-edge).

1. Podle pokynů [nainstalujte a zřiďte modul runtime Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true).

1. Pak nainstalujte agenta aktualizace zařízení z [artefaktů](https://github.com/Azure/iot-hub-device-update/releases) a teď jste připraveni spustit agenta aktualizace zařízení na zařízení IoT Edge.


### <a name="on-non-edge-iot-linux-devices"></a>Na zařízeních bez hraničních zařízení IoT Linux

Podle těchto pokynů můžete zřídit agenta aktualizace zařízení v zařízeních se systémem IoT Linux.

1. Nainstalujte službu identity IoT a přidejte nejnovější verzi do zařízení IoT. 
    1. Přihlaste se do počítače nebo zařízení IoT.
    1. Otevřete okno terminálu.
    1.  Nainstalujte nejnovější [službu IoT identity](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) na zařízení IoT pomocí tohoto příkazu:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. Zřizováním služby identity IoT získáte informace o zařízení IoT.
    * Vytvořte vlastní kopii šablony konfigurace, abychom mohli přidat informace o zřizování. V terminálu zadejte následující příkaz.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Dále upravte konfigurační soubor tak, aby zahrnoval připojovací řetězec zařízení, které chcete pro toto zařízení nebo počítač použít jako zřizovací. V terminálu zadejte následující příkaz.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Měla by se zobrazit zpráva podobná následujícímu příkladu:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagram konfiguračního souboru služby IoT identity" lightbox="media/understand-device-update/config.png":::

    1. Ve stejném okně nano Najděte blok s "ručním zřizováním pomocí připojovacího řetězce".
    1. V okně odstraňte symbol "#" před "zřizování".
    1. V okně odstraňte symbol "#" před "zdroj". 
    1. V okně odstraňte symbol "#" před "connection_string"
    1. V okně odstraňte řetězec v uvozovkách napravo od ' connection_string ' a přidejte do něj svůj připojovací řetězec. 
    1. Uložte změny do souboru pomocí CTRL + X a potom kliknutím na Y a stisknutím klávesy ENTER uložte změny. 
    
1.  Nyní použijte a restartujte službu IoT identity pomocí příkazu níže. Teď byste měli vidět "Hotovo!" výtisk, který znamená, že jste úspěšně nakonfigurovali službu IoT identity.

    > [!Note]
    > Služba identity IoT registruje identity modulů pomocí IoT Hub pomocí symetrických klíčů v současnosti.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Nakonec nainstalujte agenta aktualizace zařízení z [artefaktů](https://github.com/Azure/iot-hub-device-update/releases) a teď jste připraveni na zařízení IoT Edge spustit agenta aktualizace zařízení.


### <a name="other-iot-devices"></a>Jiná zařízení IoT

Agenta aktualizace zařízení lze také nakonfigurovat bez služby IoT identity pro testování nebo na omezená zařízení. Pomocí níže uvedených kroků zřídíte agenta aktualizace zařízení pomocí připojovacího řetězce (z modulu nebo zařízení).

1.  Nainstalujte agenta aktualizace zařízení z [artefaktů](https://github.com/Azure/iot-hub-device-update/releases).

1.  Přihlaste se k počítači nebo zařízení IoT Edge nebo zařízení IoT.
    
1.  Otevřete okno terminálu.

1.  Přidání připojovacího řetězce do [konfiguračního souboru aktualizace zařízení](device-update-configuration-file.md):
    1. V okně terminálu zadejte níže:
        - [Aktualizace balíčku](device-update-ubuntu-agent.md) se používají: sudo nano/etc/adu/adu-conf.txt
        - [Aktualizace imagí](device-update-raspberry-pi.md) použití: sudo nano/adu/adu-conf.txt
       
    1. Mělo by se zobrazit okno otevřené s nějakým textem. Po prvním zřízení agenta aktualizace zařízení na zařízení IoT odstraňte celý řetězec následující po ' connection_String = '. Je místo toho jenom zástupný text.
    
    1. V terminálu nahraďte <řetězec připojení> připojovacím řetězcem zařízení pro vaši instanci agenta aktualizace zařízení.
    
        > [!Important]
        > Nepřidávat uvozovky kolem připojovacího řetězce
        
        - connection_string =<> řetězce připojení
       
    1. Zadejte a uložte.
    
1.  Teď jste připraveni spustit agenta aktualizace zařízení na zařízení IoT Edge. 


## <a name="how-to-start-the-device-update-agent"></a>Jak spustit agenta aktualizace zařízení

Tato část popisuje, jak spustit a ověřit agenta aktualizace zařízení v případě úspěšného spuštění identity v zařízení IoT.

1.  Přihlaste se k počítači nebo zařízení s nainstalovaným agentem aktualizace zařízení.

1.  Otevřete okno terminálu a zadejte následující příkaz.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  Stav agenta můžete zjistit pomocí příkazu níže. Pokud se zobrazí nějaké problémy, přečtěte si tuto [příručku k odstraňování potíží](troubleshoot-device-update.md).
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Měl by se zobrazit stav OK.

1.  Na IoT Hubovém portálu přejdete na zařízení IoT nebo IoT Edge zařízení a zjistíte zařízení, které jste nakonfigurovali pomocí agenta aktualizace zařízení. Zobrazí se agent aktualizace zařízení spuštěný jako modul. Například:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagram názvu modulu aktualizace zařízení." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Postup sestavení a spuštění agenta aktualizace zařízení

Můžete také vytvořit a změnit vlastního agenta aktualizace zákaznických zařízení.

Podle pokynů [Sestavte](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) agenta aktualizace zařízení ze zdroje.

Po úspěšném sestavení agenta je čas [Spustit](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) agenta.

Nyní proveďte potřebné změny pro zahrnutí agenta do vaší image.  Pokyny najdete v tématu Postup [Úpravy](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) agenta aktualizace zařízení.


## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Pokud narazíte na problémy, přečtěte si příručku aktualizace zařízení IoT Hub [Průvodce řešením potíží](troubleshoot-device-update.md) , která vám pomůže odblokovat případné problémy a shromažďovat potřebné informace pro poskytování Microsoftu.


## <a name="next-steps"></a>Další kroky

Pro jednoduchou ukázku aktualizace zařízení pro IoT Hub můžete použít následující předem sestavené bitové kopie a binární soubory:

- [Aktualizace Image: Začínáme s malinou PI 3 B + referenční image Yocto](device-update-raspberry-pi.md) rozšiřitelná prostřednictvím open source, abyste si podle potřeby vytvořili vlastní image pro jinou architekturu.

- [Začínáme pomocí agenta reference simulátoru Ubuntu (18,04 x64)](device-update-simulator.md)

- [Aktualizace balíčku: Začínáme pomocí agenta balíčku Ubuntu Server 18,04 x64](device-update-ubuntu-agent.md)

- [Kurz aktualizace zařízení pro Azure IoT Hub pro Azure – reálný čas – operační systém](device-update-azure-real-time-operating-system.md)

