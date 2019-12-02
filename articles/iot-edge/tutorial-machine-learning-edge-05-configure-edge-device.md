---
title: 'Kurz: Konfigurace IoT Edge Azure IoT Edge zařízení & Machine Learning'
description: 'Kurz: konfigurace virtuálního počítače Azure se systémem Linux jako zařízení Azure IoT Edge, které funguje jako transparentní brána.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 510580dff575d60d9071667f6be28b525bc05d88
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665657"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Kurz: konfigurace zařízení IoT Edge

> [!NOTE]
> Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Pokud jste dorazili přímo do tohoto článku, doporučujeme začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) řady, abyste dosáhli nejlepších výsledků.

V tomto článku nakonfigurujeme virtuální počítač Azure se systémem Linux, aby se jednalo o Azure IoT Edge zařízení, které funguje jako transparentní brána. Transparentní konfigurace brány umožňuje zařízením připojit se k Azure IoT Hub prostřednictvím brány bez vědomí, že brána existuje. Zároveň uživatel, který pracuje se zařízeními v IoT Hub, neví o zařízení zprostředkující brány. V konečném případě používáme transparentní bránu k přidání Edge Analytics do našeho systému přidáním modulů IoT Edge do brány.

Kroky v tomto článku jsou obvykle prováděny vývojářem cloudu.

## <a name="generate-certificates"></a>Generování certifikátů

Aby zařízení fungovalo jako brána, musí být schopné bezpečně se připojit k zařízením pro příjem dat. Azure IoT Edge umožňuje použít infrastrukturu veřejných klíčů (PKI) k nastavení zabezpečených připojení mezi zařízeními. V tomto případě umožníme, aby se pro zařízení s IoT Edge připojila k zařízení, které funguje jako transparentní brána. Aby bylo možné zajistit přiměřené zabezpečení, musí zařízení pro příjem dat potvrdit identitu zařízení IoT Edge. Další informace o tom, jak IoT Edge zařízení používají certifikáty, najdete v tématu [Azure IoT Edge informace o využití certifikátu](iot-edge-certs.md).

V této části vytvoříme certifikáty podepsané svým držitelem pomocí Image Docker, kterou následně sestavíme a spustíte. Rozhodli jste se použít k dokončení tohoto kroku image Docker, která významně snižuje počet kroků potřebných k vytvoření certifikátů na vývojovém počítači s Windows. Podrobnosti o tom, jak vytvořit certifikáty bez použití kontejneru, najdete v tématu [generování certifikátů pomocí systému Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows) . [Generování certifikátů se systémem Linux](how-to-create-transparent-gateway.md#generate-certificates-with-linux) obsahuje sadu instrukcí, které provádíme automatizovaně s imagí Docker.

1. Přihlaste se ke svému vývojovému virtuálnímu počítači.

2. Otevřete příkazový řádek a spuštěním následujícího příkazu vytvořte adresář na virtuálním počítači.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Spusťte **Docker for Windows** v nabídce Start systému Windows.

4. Otevřete Visual Studio Code.

5. Vyberte **soubor** > **Otevřít složku...** a zvolte **C:\\source\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Klikněte pravým tlačítkem na souboru Dockerfile a vyberte **sestavit image**.

7. V dialogu přijměte výchozí hodnotu pro název obrázku a značku: **createcertificates: nejnovější**.

8. Počkejte na dokončení sestavení.

    > [!NOTE]
    > Může se zobrazit upozornění pro o chybějícím veřejném klíči. Toto upozornění je bezpečné ignorovat. Podobně se zobrazí upozornění zabezpečení, které doporučuje, abyste zkontrolovali a obnovili oprávnění k imagi, což je bezpečné pro tento obrázek ignorovat.

9. V okně Visual Studio Code terminálu spusťte kontejner createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker vás vyzve pro přístup k jednotce **c:\\** . Vyberte možnost **sdílet**.

11. Po zobrazení výzvy zadejte své přihlašovací údaje.

12. Po dokončení kontejneru vyhledejte v jazyce c následující soubory **:\\edgeCertificates**:

    * c:\\edgeCertificates\\ch certifikátů\\Azure-IoT-test-Only. root. ca. CERT. pem
    * c:\\edgeCertificates\\ch certifikátů\\New-Edge-Device-full-Chain. CERT. pem
    * c:\\edgeCertificates\\ch certifikátů\\New-Edge-Device. CERT. pem
    * c:\\edgeCertificates\\ch certifikátů\\New-Edge-Device. CERT. pfx
    * c:\\edgeCertificates\\Private\\New-Edge-Device. Key. pem

## <a name="upload-certificates-to-azure-key-vault"></a>Nahrání certifikátů do Azure Key Vault

K bezpečnému uložení certifikátů a jejich zpřístupnění z více zařízení odešleme certifikáty do Azure Key Vault. Jak vidíte v seznamu výše, máme dva typy souborů certifikátu: PFX a PEM. Soubor PFX budeme považovat za Key Vault certifikáty, které se mají nahrát do Key Vault. Soubory PEM jsou prostý text a my je považujeme za Key Vault tajných klíčů. Použijeme Key Vault přidružené k pracovnímu prostoru Azure Machine Learning, který jsme vytvořili spuštěním [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Z [Azure Portal](https://portal.azure.com)přejděte do pracovního prostoru Azure Machine Learning.

2. Na stránce Přehled v pracovním prostoru Azure Machine Learning vyhledejte název **Key Vault**.

    ![Kopírovat název trezoru klíčů](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Na svém vývojovém počítači nahrajte certifikáty do Key Vault. Pomocí informací o prostředku nahraďte **\<subscriptionId\>** a **\<ho trezoru klíčů\>** .

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Pokud se zobrazí výzva, přihlaste se k Azure.

5. Skript se spustí po dobu několika minut s výstupem, ve kterém se zobrazí nové položky Key Vault.

    ![Výstup skriptu Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Vytvoření zařízení IoT Edge

Pokud chcete zařízení Azure IoT Edge připojit ke službě IoT Hub, nejdřív v centru vytvoříme identitu pro zařízení. Připojovací řetězec přijímáme z identity zařízení v cloudu a používá ho ke konfiguraci modulu runtime na našem IoT Edgem zařízení. Jakmile je zařízení nakonfigurované a připojuje se k rozbočovači, můžeme nasadit moduly a odesílat zprávy. Konfiguraci fyzického IoT Edge zařízení můžeme změnit také tak, že změníte konfiguraci odpovídající identity zařízení ve službě IoT Hub.

V tomto kurzu vytvoříme novou identitu zařízení pomocí Visual Studio Code. Tyto kroky můžete provést také pomocí [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)nebo rozhraní příkazového [řádku Azure CLI](how-to-register-device.md#register-with-the-azure-cli).

1. Na svém vývojovém počítači otevřete Visual Studio Code.

2. V zobrazení Průzkumník Visual Studio Code otevřete rámec **zařízení Azure IoT Hub** .

3. Klikněte na tři tečky a vyberte **vytvořit IoT Edge zařízení**.

4. Dejte zařízení název. Pro usnadnění práce používáme **aaTurbofanEdgeDevice** , takže seřadí před všemi klientskými zařízeními, která jsme vytvořili dřív prostřednictvím zařízení, aby odesílala testovací data.

5. Nové zařízení se zobrazí v seznamu zařízení.

    ![Zobrazit nové aaTurbofanEdgeDevice v Průzkumníkovi VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Nasazení virtuálního počítače Azure

K vytvoření našeho IoT Edge zařízení pro tento kurz používáme image [Azure IoT Edge na Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) z Azure Marketplace. Azure IoT Edge image Ubuntu nainstaluje nejnovější modul runtime Azure IoT Edge a jeho závislosti při spuštění. Virtuální počítač nasadíme pomocí skriptu PowerShellu, `Create-EdgeVM.ps1`; Šablona Správce prostředků, `IoTEdgeVMTemplate.json`; a skript prostředí `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Povolit programové nasazení

Pro použití image z webu Marketplace ve skriptovém nasazení musíme pro Image povolit programové nasazení.

1. Přihlaste se k portálu Azure.

1. Vyberte **Všechny služby**.

1. Na panelu hledání zadejte a vyberte **Marketplace**.

1. Na panelu hledání zadejte a vyberte **Azure IoT Edge v Ubuntu**.

1. Vyberte, který **chcete nasadit programově?** Hypertextový odkaz Začínáme.

1. Klikněte na tlačítko **Povolit** a pak na **Uložit**.

    ![Povolit programové nasazení pro virtuální počítač](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Zobrazí se oznámení o úspěchu.

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Potom spuštěním skriptu vytvořte virtuální počítač pro zařízení IoT Edge.

1. Otevřete okno PowerShellu a přejděte do adresáře **EdgeVM** .

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Spusťte skript pro vytvoření virtuálního počítače.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Po zobrazení výzvy zadejte hodnoty pro každý parametr. Pro předplatné, skupinu prostředků a umístění doporučujeme, abyste používali stejný jako u všech prostředků v rámci tohoto kurzu.

    * **ID předplatného Azure**: našlo se v Azure Portal.
    * **Název skupiny prostředků**: zapamatovatelné názvy pro seskupení prostředků pro tento kurz
    * **Umístění**: umístění Azure, ve kterém se virtuální počítač vytvoří. Například westus2 nebo northeurope. Další informace najdete v tématu všechna [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: název účtu správce, který použijete pro přihlášení k virtuálnímu počítači.
    * **AdminPassword**: heslo, které se má nastavit pro AdminUsername ve virtuálním počítači.

4. Aby se skript mohl nastavit jako virtuální počítač, musíte se přihlásit k Azure pomocí přihlašovacích údajů přidružených k předplatnému Azure, které používáte.

5. Skript potvrdí informace pro vytvoření virtuálního počítače. Pokračujte výběrem **y** nebo **ENTER** .

6. Skript se spustí několik minut, protože provede následující kroky:

    * Vytvořte skupinu prostředků, pokud již neexistuje.
    * Vytvoření virtuálního počítače
    * Přidejte výjimky NSG pro virtuální počítač pro porty 22 (SSH), 5671 (AMQP), 5672 (AMPQ) a 443 (SSL).
    * Instalace rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

7. Skript vytvoří výstup připojovacího řetězce SSH pro připojení k virtuálnímu počítači. Zkopírujte připojovací řetězec pro další krok.

    ![Zkopírování připojovacího řetězce SSH pro virtuální počítač](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Připojení k zařízení IoT Edge

V následujících částech se konfiguruje virtuální počítač Azure, který jsme vytvořili. Prvním krokem je připojení k virtuálnímu počítači.

1. Otevřete příkazový řádek a vložte připojovací řetězec SSH, který jste zkopírovali z výstupu skriptu. Zadejte vlastní informace pro uživatelské jméno, příponu a oblast podle hodnot, které jste zadali do skriptu PowerShellu v předchozí části.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Po zobrazení výzvy k ověření pravosti hostitele zadejte **Ano** a vyberte **zadat**.

3. Po zobrazení výzvy zadejte heslo.

4. Ubuntu zobrazí uvítací zprávu a pak by se měla zobrazit výzva jako `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Stažení Key Vault certifikátů

Dříve v tomto článku jsme nahráli certifikáty, které Key Vault, aby byly k dispozici pro naše IoT Edge zařízení a naše listová zařízení, což je zařízení pro příjem dat, které používá IoT Edge zařízení jako bránu ke komunikaci s IoT Hub. Později v tomto kurzu budeme pracovat se listovým zařízením. V této části si stáhněte certifikáty do zařízení IoT Edge.

1. Z relace SSH na virtuálním počítači se systémem Linux se přihlaste k Azure pomocí Azure CLI.

    ```bash
    az login
    ```

1. Zobrazí se výzva k otevření prohlížeče pro <https://microsoft.com/devicelogin> a zadání jedinečného kódu. Tyto kroky můžete provést na svém místním počítači. Až budete s ověřováním hotovi, zavřete okno prohlížeče.

1. Po úspěšném ověření se virtuální počítač se systémem Linux přihlásí a vypíše Vaše předplatná Azure.

1. ASet předplatné Azure, které chcete použít pro příkazy rozhraní příkazového řádku Azure CLI.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Vytvořte na virtuálním počítači adresář pro certifikáty.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Stáhněte si certifikáty, které jste uložili v trezoru klíčů: New-Edge-Device-full-Chain. CERT. pem, New-Edge-Device. Key. pem a Azure-IoT-test-Only. root. ca. CERT. pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualizace konfigurace zařízení IoT Edge

Modul runtime IoT Edge používá soubor/etc/iotedge/config.yaml k uchování jeho konfigurace. V tomto souboru musíme aktualizovat tři části informací:

* **Připojovací řetězec zařízení**: připojovací řetězec z identity tohoto zařízení v IoT Hub
* **Certifikáty:** certifikáty, které se mají použít pro připojení s podřízenými zařízeními.
* **Název hostitele:** plně kvalifikovaný název domény (FQDN) virtuálního počítače IoT Edge zařízení.

*Azure IoT Edge image Ubuntu* , kterou jsme použili k vytvoření virtuálního počítače s IoT Edge, přichází pomocí skriptu prostředí, který aktualizuje config. yaml připojovacím řetězcem.

1. V Visual Studio Code klikněte pravým tlačítkem na zařízení IoT Edge a pak vyberte **Kopírovat připojovací řetězec zařízení**.

    ![Kopírovat připojovací řetězec z Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. V relaci SSH spuštěním příkazu aktualizujte soubor config. yaml pomocí připojovacího řetězce zařízení.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

V dalším kroku aktualizujeme certifikáty a název hostitele přímým úpravou souboru config. yaml.

1. Otevřete soubor config. yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Aktualizujte část certifikáty souboru config. yaml tak, že odeberete úvodní `#` a nanastavíte cestu tak, aby soubor vypadal jako v následujícím příkladu:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Ujistěte se, že "certifikáty:" nemají žádné předchozí prázdné znaky a že každý z nich předchází dva prostory.

    Kliknutím pravým tlačítkem v nano dojde k vložení obsahu schránky do aktuální pozice kurzoru. Pokud chcete řetězec nahradit, pomocí šipek klávesnice přejděte k řetězci, který chcete nahradit, odstraňte řetězec a pak klikněte pravým tlačítkem na vložení z vyrovnávací paměti.

3. V Azure Portal přejděte na svůj virtuální počítač. Zkopírujte název DNS (plně kvalifikovaný název domény počítače) z části **Přehled** .

4. Vložte plně kvalifikovaný název domény do oddílu hostname v souboru config. yml. Ujistěte se, že název je malý.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Soubor uložte a zavřete (`Ctrl + X`, `Y``Enter`).

6. Restartujte démon iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Ověřte stav procesu démona IoT Edge (za příkazem zadejte ": q", který chcete ukončit).

    ```bash
    systemctl status iotedge
    ```

8. Pokud se zobrazí chyby (barevný text s předponou "\[ERROR\]"), podrobnější informace o chybě najdete v protokolech pro prohlédnutí stavu.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Další kroky

Právě jsme dokončili konfiguraci virtuálního počítače Azure jako Azure IoT Edge transparentní brány. Začali jsme vygenerováním testovacích certifikátů, které jsme nahráli do Azure Key Vault. V dalším kroku jsme k nasazení virtuálního počítače s imagí "Ubuntu Server 16,04 LTS Azure IoT Edge + runtime" z webu Azure Marketplace použili skript a šablonu Správce prostředků. Skript převzal další krok při instalaci rozhraní příkazového řádku Azure CLI ([instalace Azure CLI pomocí apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). Když je virtuální počítač spuštěný a spuštěný, připojili jsme se přes SSH, který jste přihlásili k Azure, stáhli certifikáty z Key Vault a provedli jsme několik aktualizací konfigurace IoT Edge běhu prostřednictvím aktualizace souboru config. yaml. Další informace o použití IoT Edge jako brány najdete v tématu [jak se dá zařízení IoT Edge použít jako brána](iot-edge-as-gateway.md). Další informace o tom, jak nakonfigurovat zařízení IoT Edge jako transparentní bránu, najdete v tématu [Konfigurace zařízení IoT Edge, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md).

Pokračujte dalším článkem a Sestavujte IoT Edge moduly.

> [!div class="nextstepaction"]
> [Vytvoření a nasazení vlastních modulů IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)
