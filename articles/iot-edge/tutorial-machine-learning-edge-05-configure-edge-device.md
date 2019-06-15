---
title: Konfigurace zařízení IoT Edge – Machine Learning v Azure IoT Edge | Dokumentace Microsoftu
description: Nakonfigurujte virtuální počítač Azure s Linuxem jako zařízení Azure IoT Edge, který funguje jako transparentní brána.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 86efbaa053e087885f0dbfea24000781d41ac5d5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057712"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Kurz: Konfigurace zařízení IoT Edge

> [!NOTE]
> Tento článek je součástí série kurz o používání Azure Machine learningu na hraničních zařízeních IoT. Pokud jste jste dostali přímo v tomto článku, doporučujeme vám začneme [nejprve článek](tutorial-machine-learning-edge-01-intro.md) v řadě nejlepších výsledků.

V tomto článku jsme konfiguraci virtuálního počítače Azure s Linuxem bude zařízení Azure IoT Edge, který funguje jako transparentní brána. Konfigurace transparentní brána umožňuje zařízením připojení k Azure IoT Hub přes bránu nainstalovat bez mého, existující bránu. Ve stejnou dobu uživatelské interakce s zařízení ve službě IoT Hub Nepozná zařízení zprostředkující brány. Nakonec jsme použít transparentní brány přidáním modulů IoT Edge do brány přidat hraniční analýzu na náš systém.

Kroky v tomto článku se obvykle provádí vývojář cloudu.

## <a name="generate-certificates"></a>Generování certifikátů

Pro zařízení, aby fungoval jako brána, je potřeba mít možnost bezpečně připojte k podřízené zařízení. Azure IoT Edge umožňuje používat infrastrukturu veřejných klíčů (PKI) nastavení zabezpečeného připojení mezi zařízeními. V tomto případě jsme se umožní příjem dat zařízení pro připojení k zařízení IoT Edge sloužit jako transparentní brána. Zachování přiměřené zabezpečení, by měl podřízené zařízení potvrdit identitu zařízení IoT Edge. Další informace o tom, jak zařízení IoT Edge používat certifikáty, najdete v části [podrobnosti o použití certifikátu Azure IoT Edge](iot-edge-certs.md).

V této části se nám vytvořit certifikáty podepsané svým držitelem pomocí image Dockeru, který jsme potom sestavíte a spustíte. Jsme se rozhodli použít image Dockeru k dokončení tohoto kroku, jak významně snižuje počet kroků je potřeba k vytvoření certifikátů na vývojovém počítači s Windows. Zobrazit [generování certifikátů s Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows) podrobnosti o tom, jak vytvářet certifikáty bez použití kontejneru. [Generování certifikátů s Linuxem](how-to-create-transparent-gateway.md#generate-certificates-with-linux) má sadu instrukcí, které jsme automatických s image Dockeru.

1. Přihlaste se k virtuálnímu počítači vývoje.

2. Otevřete příkazový řádek příkazový řádek a spusťte následující příkaz k vytvoření adresáře ve virtuálním počítači.

    ```cmd
    mkdir C:\edgecertificates
    ```

3. Spustit **Docker pro Windows** v nabídce Windows Start.

4. Otevřete Visual Studio Code.

5. Vyberte **souboru** > **otevřete složku...**  a zvolte **C:\\zdroj\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Klikněte pravým tlačítkem na soubor dockerfile a zvolte **sestavení Image**.

7. V dialogovém okně, přijměte výchozí hodnotu pro název image a značky: **createcertificates:latest**.

8. Počkejte na dokončení sestavení.

    > [!NOTE]
    > Může se zobrazit upozornění pro chybějící veřejný klíč. Je bezpečné ignorovat toto upozornění. Podobně zobrazí se upozornění zabezpečení, doporučí, abyste kontrola/resetování oprávnění v obrázku, který je bezpečné ignorovat pro tuto bitovou kopii.

9. V okně terminálu Visual Studio Code spuštěním createcertificates kontejneru.

    ```cmd
    docker run --name createcertificates --rm -v
    C:\edgeCertificates:/edgeCertificates createcertificates
    /edgeCertificates
    ```

10. Docker zobrazí výzvu pro přístup k **C:\\**  jednotky. Vyberte **nesdílíme data se**.

11. Zadejte svoje přihlašovací údaje po zobrazení výzvy.

12. Jednou kontejner dokončí spuštěné, zkontrolujte následující soubory v **c:\\edgecertificates**:

    * C:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * C:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * C:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * C:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * C:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Nahrát certifikáty pro Azure Key Vault

Naše certifikáty bezpečně uložte a zajistíte jejich přístupnost z více zařízení, jsme se nahrát certifikáty do služby Azure Key Vault. Jak je vidět v seznamu výše, máme dva typy souborů certifikátu: Soubor PFX a PEM. Společnost Microsoft bude považovat za soubor PFX Key Vault Certificates k odeslání do služby Key Vault. Soubory PEM jsou ve formátu prostého textu a jsme se s nimi zacházet jako tajné kódy Key Vault. Budeme používat služby Key Vault, který je přidružený k pracovnímu prostoru služby Azure Machine Learning jsme vytvořili spuštěním [poznámkových bloků Azure](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Z [webu Azure portal](https://portal.azure.com), přejděte do pracovního prostoru služby Azure Machine Learning.

2. Na stránce Přehled pracovního prostoru Azure Machine Learning služby najít název **služby Key Vault**.

    ![Zkopírujte název trezoru klíčů](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Na svém vývojovém počítači nahrajte certifikáty do služby Key Vault. Nahraďte **\<subscriptionId\>** a **\<keyvaultname\>** s informacemi o prostředku.

    ```powershell
    C:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1
    -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Pokud se zobrazí výzva, přihlaste se k Azure.

5. Bude skript spuštěn pro několik minut, než se výstup, který uvádí nové položky služby Key Vault.

    ![Výstup skriptu služby Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Vytvoření zařízení IoT Edge

Připojit zařízení s Azure IoT Edge do služby IoT hub, vytvoříme nejprve identitu zařízení v centru. Jsme trvat připojovacího řetězce z identitu zařízení v cloudu a použít ho ke konfiguraci modulu runtime naše zařízení IoT Edge. Jakmile se zařízení byl nakonfigurován a připojí k centru, jsme schopní nasadit moduly a odesílání zpráv. Konfigurace fyzického zařízení IoT Edge jsme také můžete změnit změnou konfigurace odpovídající identitu zařízení ve službě IoT hub.

V tomto kurzu vytvoříme novou identitu zařízení pomocí nástroje Visual Studio Code. Můžete také použít tento postup pomocí [webu Azure portal](how-to-register-device-portal.md), nebo [rozhraní příkazového řádku Azure](how-to-register-device-cli.md).

1. Na svém vývojovém počítači otevřete Visual Studio Code.

2. Otevřít **zařízení Azure IoT Hub** snímek v zobrazení Průzkumníka Visual Studio Code.

3. Klikněte na tlačítko se třemi tečkami a vyberte **vytvoření zařízení IoT Edge**.

4. Zadejte název zařízení. Pro usnadnění práce jsme použít **aaTurbofanEdgeDevice** tak řadí před všechny klientských zařízení, která jsme vytvořili dříve prostřednictvím prostředí zařízení k odesílání dat testu.

5. Nové zařízení se zobrazí v seznamu zařízení.

    ![Zobrazení nového aaTurbofanEdgeDevice v Průzkumníku VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Nasazení virtuálních počítačů Azure

Používáme [Azure IoT Edge na Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) image z Azure Marketplace k vytvoření naší zařízení IoT Edge pro účely tohoto kurzu. Azure IoT Edge na Ubuntu image nainstaluje nejnovější modul runtime Azure IoT Edge a jeho závislostí při spuštění. Můžeme nasadit virtuální počítač pomocí skriptu prostředí PowerShell `Create-EdgeVM.ps1`; šablonu Resource Manageru `IoTEdgeVMTemplate.json`; a skript prostředí, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Povolit programové nasazení

Chcete-li použít image z Tržiště v popisují nasazení, musíme povolit programové nasazení bitové kopie.

1. Přihlaste se k portálu Azure.

1. Vyberte **Všechny služby**.

1. Na panelu hledání zadejte a vyberte **Marketplace**.

1. Na panelu hledání zadejte a vyberte **Azure IoT Edge na Ubuntu**.

1. Vyberte **chcete nasadit prostřednictvím kódu programu? Začínáme** hypertextový odkaz.

1. Vyberte **povolit** tlačítko, pak **Uložit**.

    ![Povolit programové nasazení pro virtuální počítač](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Zobrazí se oznámení o úspěchu.

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

V dalším kroku spusťte skript k vytvoření virtuálního počítače pro zařízení IoT Edge.

1. Otevřete okno Powershellu a přejděte **EdgeVM** adresáře.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\EdgeVM”
    ```

2. Spusťte skript k vytvoření virtuálního počítače.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Po zobrazení výzvy zadejte hodnoty pro každý parametr. Pro předplatné skupinu prostředků a umístění, doporučujeme použít stejné jako pro všechny prostředky v celém tomto kurzu.

    * **ID předplatného Azure**: najít na webu Azure Portal
    * **Název skupiny prostředků**: snadno zapamatovatelný název pro seskupení prostředků pro účely tohoto kurzu
    * **Umístění**: Umístění Azure, ve kterém se vytvoří virtuální počítač. Například westus2 nebo northeurope. Další informace najdete v tématu všechny [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: název pro účet správce, kterou použijete k přihlášení k virtuálnímu počítači
    * **AdminPassword**: heslo nastavit pro AdminUsername na virtuálním počítači

4. Pro skript, který chcete moci vytvořit virtuální počítač budete muset přihlásit k Azure pomocí přihlašovacích údajů spojené s předplatným Azure, kterou používáte.

5. Skript potvrdí informace pro vytvoření virtuálního počítače. Vyberte **y** nebo **Enter** pokračujte.

6. Skript se spustí pro několik minut, než jak je provede následující kroky:

    * Pokud ho ještě neexistuje, vytvořte skupinu prostředků
    * Vytvoření virtuálního počítače
    * Přidat výjimky skupiny zabezpečení sítě pro virtuální počítač pro porty 22 (SSH), 5671 (AMQP), 5672 (AMPQ) a 443 (SSL)
    * Nainstalujte [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. Skript vypíše připojovací řetězec SSH pro připojení k virtuálnímu počítači. Zkopírujte připojovací řetězec na další krok.

    ![Zkopírujte připojovací řetězec SSH pro virtuální počítač](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Připojte se k zařízení IoT Edge

Další několika oddílů konfigurace virtuálního počítače Azure, který jsme vytvořili. Prvním krokem je připojení k virtuálnímu počítači.

1. Otevřete příkazový řádek a vložte připojovací řetězec SSH, který jste zkopírovali z výstupu skriptu. Zadejte vlastní informace o zadání uživatelského jména, přípony a oblasti podle hodnoty, které jste zadali do skriptu Powershellu v předchozí části.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Po zobrazení výzvy k ověření pravosti hostitele, zadejte **Ano** a vyberte **Enter**.

3. Po zobrazení výzvy zadejte heslo.

4. Ubuntu zobrazí uvítací zprávu a pak byste měli vidět příkazový řádek jako `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Stáhnout certifikáty služby Key Vault

Dříve v tomto článku jsme nahráli certifikáty do služby Key Vault, aby byly dostupné pro naše zařízení IoT Edge a naše zařízení typu list, které je příjem dat zařízení, která používá zařízení IoT Edge jako brána ke komunikaci s centrem IoT. Bude se zabýváme zařízení typu list později v tomto kurzu. V této části stáhněte certifikáty do zařízení IoT Edge.

1. Z relace SSH na virtuálním počítači s Linuxem Přihlaste se k Azure pomocí Azure CLI.

    ```bash
    az login
    ```

1. Zobrazí se výzva k prohlížeči otevřete <https://microsoft.com/devicelogin> a zadejte jedinečný kód. Tyto kroky můžete provést na místním počítači. Jakmile budete hotovi, zavřete okno prohlížeče ověřování.

1. Když úspěšně ověřit, virtuálního počítače s Linuxem se přihlaste a seznam vašich předplatných Azure.

1. Sada předplatné Azure, kterou chcete použít pro příkazy Azure CLI.

    ```bash
    az account set --subscription <subscription id>
    ```

1. Vytvořte adresář na virtuálním počítači pro certifikáty.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Stáhnout certifikáty, které ukládají ve službě key vault: nové edge – zařízení full-chain.cert.pem, nové device.key.pem hrany a azure-iot-test-only.root.ca.cert.pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualizace konfigurace zařízení IoT Edge

Modul runtime IoT Edge /etc/iotedge/config.yaml soubor používá k zachování její konfiguraci. Musíme aktualizovat tři údaje v tomto souboru:

* **Připojovací řetězec zařízení**: připojovací řetězec z identitu tohoto zařízení ve službě IoT Hub
* **Certifikáty:** certifikáty, které budou používat pro připojení s příjem dat zařízení
* **Název hostitele:** plně kvalifikovaný název domény (FQDN) ze zařízení IoT Edge virtuálního počítače.

*Azure IoT Edge na Ubuntu* image, kterou jste použili k vytvoření virtuálního počítače IoT Edge je součástí skript prostředí, která aktualizuje config.yaml připojovacím řetězcem.

1. Ve Visual Studio Code klikněte pravým tlačítkem na zařízení IoT Edge a pak vyberte **připojovací řetězec zařízení kopírování**.

    ![Zkopírujte připojovací řetězec z Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. V relaci SSH spusťte příkaz k aktualizaci souboru config.yaml vaším připojovacím řetězcem zařízení.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Dále budeme přímou úpravou config.yaml aktualizovat certifikáty a název hostitele.

1. Otevřete soubor config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Aktualizujte část certifikáty config.yaml odebráním úvodního `#` a nastavení cesty tak, že soubor vypadá jako v následujícím příkladu:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Ujistěte se, že "certifikáty:" nemá žádné předchozí prázdné znaky a že jednotlivé certifikáty předchází dvě mezery.

    Pravým tlačítkem myši v nano se vložit obsah schránky do aktuální pozici kurzoru. K nahrazení řetězce, pomocí vaší šipkových kláves můžete přejít na řetězec, který chcete nahradit, odstraňte řetězec a pak klikněte pravým tlačítkem a vložte z vyrovnávací paměti.

3. Na webu Azure Portal přejděte ke svému virtuálnímu počítači. Zkopírujte název DNS (plně kvalifikovaný název domény počítače) z **přehled** oddílu.

4. Vložte do části názvu hostitele config.yml plně kvalifikovaný název domény. Ujistěte se, zda je název malými písmeny.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Uložte a zavřete soubor (`Ctrl + X`, `Y`, `Enter`).

6. Restartujte démona iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Kontrola stavu démona IoT Edge (po příkazu, zadejte ": q" ukončit).

    ```bash
    systemctl status iotedge
    ```

8. Pokud se zobrazí chyby (vybarvenými text s předponou "\[chyba\]") v protokolech vyhledejte proces démon stav podrobné informace o chybě.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Další postup

Právě dokončili konfiguraci virtuálního počítače Azure jako transparentní brána Azure IoT Edge. Začali jsme generováním testovací certifikáty, které jsme nahráli do Azure Key Vault. Dále jsme použili skript a šablony Resource Manageru k nasazení virtuálních počítačů s "Ubuntu Server 16.04 LTS a modul runtime Azure IoT Edge" image z Azure marketplace. Trvalo další krok instalace rozhraní příkazového řádku Azure ([instalace Azure CLI pomocí apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). S virtuálního počítače se a spouštění jsme připojit přes SSH, přihlášení k Azure, stáhnout certifikáty ze služby Key Vault a provedli několik aktualizací v konfiguraci modulu Runtime IoT Edge prostřednictvím aktualizace souboru config.yaml. Další informace o použití IoT Edge jako brána najdete v tématu [zařízení jak IoT Edge může sloužit jako brána](iot-edge-as-gateway.md). Další informace o tom, jak nakonfigurovat nastavení zařízení IoT Edge jako transparentní brána najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge tak, aby fungoval jako transparentní brána](how-to-create-transparent-gateway.md).

Pokračujte k dalšímu článku vytvářet moduly IoT Edge.

> [!div class="nextstepaction"]
> [Vytvoření a nasazení vlastních modulů IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)
