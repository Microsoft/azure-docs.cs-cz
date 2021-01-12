---
title: 'Kurz: Konfigurace IoT Edge Machine Learning zařízení v Azure IoT Edge'
description: V tomto kurzu nakonfigurujete virtuální počítač Azure se systémem Linux jako Azure IoT Edge zařízení, které funguje jako transparentní brána.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: 74d77d8c81455116cec861bf6704c6cb96526561
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121086"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Kurz: konfigurace zařízení IoT Edge

V tomto článku nakonfigurujeme virtuální počítač Azure se systémem Linux, aby se jednalo o IoT Edge zařízení, které funguje jako transparentní brána. Transparentní konfigurace brány umožňuje zařízením připojit se k Azure IoT Hub prostřednictvím brány bez vědomí, že brána existuje. Současně uživatel, který pracuje se zařízeními v Azure IoT Hub, neví, že zařízení zprostředkující brány je v provozu. Nakonec přidáme do našeho systému Edge Analytics tak, že do transparentní brány přidáte IoT Edge moduly.

Kroky v tomto článku jsou obvykle prováděny vývojářem cloudu.

V této části kurzu se dozvíte, jak:

> [!div class="checklist"]
>
> * Vytvořte certifikáty, aby bylo možné zařízení brány bezpečně připojit k zařízením pro příjem dat.
> * Vytvořte zařízení IoT Edge.
> * Vytvořte virtuální počítač Azure pro simulaci zařízení IoT Edge.

## <a name="prerequisites"></a>Požadavky

Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Každý článek v sérii vychází z práce v předchozím článku. Pokud jste dorazili přímo do tohoto článku, přejděte na [první článek](tutorial-machine-learning-edge-01-intro.md) v řadě.

## <a name="create-certificates"></a>Vytvoření certifikátů

Aby zařízení fungovalo jako brána, musí být schopné bezpečně se připojit k zařízením pro příjem dat. Azure IoT Edge umožňuje použít infrastrukturu veřejných klíčů (PKI) k nastavení zabezpečených připojení mezi zařízeními. V tomto případě umožníme zařízení IoT pro příjem připojení k IoT Edgemu zařízení, které funguje jako transparentní brána. Aby bylo možné zajistit přiměřené zabezpečení, musí zařízení pro příjem dat potvrdit identitu zařízení IoT Edge. Další informace o tom, jak IoT Edge zařízení používají certifikáty, najdete v tématu [Azure IoT Edge informace o využití certifikátu](iot-edge-certs.md).

V této části vytvoříme certifikáty podepsané svým držitelem pomocí Image Docker, kterou následně sestavíme a spustíte. Rozhodli jste se použít k dokončení tohoto kroku image Docker, protože významně snižuje počet kroků potřebných k vytvoření certifikátů na vývojovém počítači s Windows. Podívejte se na téma [Vytvoření ukázkových certifikátů, ve kterém můžete testovat IoT Edge funkce zařízení](how-to-create-test-certificates.md) a pochopit, co jsme s imagí Docker provedli.

1. Přihlaste se ke svému vývojářskému VIRTUÁLNÍmu počítači.

2. Vytvořte novou složku s cestou a názvem `c:\edgeCertificates` .

3. Pokud ještě není spuštěný, spusťte **Docker for Windows** v nabídce Start systému Windows.

4. Otevřete Visual Studio Code.

5. Vyberte **soubor**  >  **Otevřít složku...** a zvolte **C: \\ source \\ IoTEdgeAndMlSample \\ CreateCertificates**.

6. V podokně Průzkumník klikněte pravým tlačítkem na **souboru Dockerfile** a vyberte **sestavit image**.

7. V dialogu přijměte výchozí hodnotu pro název obrázku a značku: **createcertificates: nejnovější**.

    ![Vytvoření certifikátů v Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Počkejte na dokončení sestavení.

    > [!NOTE]
    > Může se zobrazit upozornění pro o chybějícím veřejném klíči. Toto upozornění je bezpečné ignorovat. Podobně se zobrazí upozornění zabezpečení, které doporučuje, abyste zkontrolovali a obnovili oprávnění k imagi, což je bezpečné pro tento obrázek ignorovat.

9. V okně Visual Studio Code terminálu spusťte kontejner createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker vás vyzve pro přístup k jednotce **c: \\** . Vyberte možnost **sdílet**.

11. Po zobrazení výzvy zadejte své přihlašovací údaje.

12. Po dokončení kontejneru vyhledejte v **c: \\ edgeCertificates** následující soubory:

    * c: \\ edgeCertificates \\ certifikáty \\ Azure-IoT-test-Only. root. ca. CERT. pem
    * c: \\ edgeCertificates \\ certifikáty \\ New-Edge-Device-full-Chain. CERT. pem
    * c: \\ edgeCertificates \\ certifikáty \\ New-Edge-Device. CERT. pem
    * c: \\ edgeCertificates \\ certifikáty \\ New-Edge-Device. CERT. pfx
    * c: \\ edgeCertificates \\ Private \\ New-Edge-Device. Key. pem

## <a name="upload-certificates-to-azure-key-vault"></a>Nahrání certifikátů do Azure Key Vault

K bezpečnému uložení certifikátů a jejich zpřístupnění z více zařízení odešleme certifikáty do Azure Key Vault. Jak vidíte v seznamu výše, máme dva typy souborů certifikátu: PFX a PEM. Soubor PFX budeme považovat za Key Vault certifikáty, které se mají nahrát do Key Vault. Soubory PEM jsou prostý text a my je považujeme za Key Vault tajných klíčů. Použijeme Key Vault přidružené k pracovnímu prostoru Azure Machine Learning, který jsme vytvořili spuštěním [poznámkových bloků Jupyter](tutorial-machine-learning-edge-04-train-model.md#run-jupyter-notebooks).

1. Z [Azure Portal](https://portal.azure.com)přejděte do pracovního prostoru Azure Machine Learning.

2. Na stránce Přehled v pracovním prostoru Azure Machine Learning vyhledejte název **Key Vault**.

    ![Kopírovat název trezoru klíčů](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Na svém vývojovém počítači nahrajte certifikáty do Key Vault. Nahraďte **\<subscriptionId\>** a **\<keyvaultname\>** informacemi o prostředcích.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Pokud se zobrazí výzva, přihlaste se k Azure.

5. Skript se spustí po dobu několika minut s výstupem, ve kterém se zobrazí nové položky Key Vault.

    ![Výstup skriptu Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Vytvoření zařízení IoT Edge

Pokud chcete zařízení Azure IoT Edge připojit ke službě IoT Hub, nejdřív v centru vytvoříme identitu pro zařízení. Připojovací řetězec přijímáme z identity zařízení v cloudu a používá ho ke konfiguraci modulu runtime na našem IoT Edgem zařízení. Jakmile se nakonfigurované zařízení připojí k centru, můžeme nasadit moduly a poslat zprávy. Konfiguraci fyzického IoT Edge zařízení můžeme také změnit změnou odpovídající identity zařízení ve službě IoT Hub.

V tomto kurzu vytvoříme novou identitu zařízení pomocí Visual Studio Code. Tento postup můžete provést také pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI.

1. Na svém vývojovém počítači otevřete Visual Studio Code.

2. Rozbalte rámec **Azure IoT Hub** v zobrazení Průzkumník Visual Studio Code.

3. Klikněte na tři tečky a vyberte **vytvořit IoT Edge zařízení**.

4. Dejte zařízení název. Pro usnadnění práce používáme název **aaTurbofanEdgeDevice** , takže se seřadí na začátek seznamu zařízení.

5. Nové zařízení se zobrazí v seznamu zařízení.

    ![Zobrazit nové aaTurbofanEdgeDevice v Průzkumníkovi VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Nasazení virtuálního počítače Azure

K vytvoření našeho IoT Edge zařízení pro tento kurz používáme image [Azure IoT Edge na Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) z Azure Marketplace. Azure IoT Edge image Ubuntu nainstaluje nejnovější modul runtime Azure IoT Edge a jeho závislosti při spuštění. Virtuální počítač nasadíme pomocí skriptu PowerShellu, a `Create-EdgeVM.ps1` Správce prostředků šablony, `IoTEdgeVMTemplate.json` a skriptu prostředí `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Povolit programové nasazení

Pro použití image z webu Marketplace ve skriptovém nasazení musíme pro Image povolit programové nasazení.

1. Přihlaste se k webu Azure Portal.

1. Vyberte **Všechny služby**.

1. Na panelu hledání zadejte a vyberte **Marketplace**.

1. Na panelu hledání na webu Marketplace zadejte a vyberte **Azure IoT Edge v Ubuntu**.

1. Vyberte **hypertextový odkaz Začínáme,** který chcete nasadit programově.

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
    * Přidání výjimek NSG pro virtuální počítač pro porty 22 (SSH), 5671 (AMQP), 5672 (AMPQ) a 443 (TLS)
    * Instalace rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli-apt)

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

4. Ubuntu zobrazí uvítací zprávu a pak by se měla zobrazit výzva jako `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Stažení Key Vault certifikátů

Dříve v tomto článku jsme nahráli certifikáty, které Key Vault, aby byly k dispozici pro naše IoT Edge zařízení a naše listová zařízení. Listové zařízení je zařízení, které používá IoT Edge zařízení jako brána ke komunikaci s IoT Hub.

Později v tomto kurzu budeme pracovat se listovým zařízením. V této části si stáhněte certifikáty do zařízení IoT Edge.

1. Z relace SSH na virtuálním počítači se systémem Linux se přihlaste k Azure pomocí Azure CLI.

    ```azurecli
    az login
    ```

1. Zobrazí se výzva k otevření prohlížeče <https://microsoft.com/devicelogin> a zadání jedinečného kódu. Tyto kroky můžete provést na svém místním počítači. Až budete s ověřováním hotovi, zavřete okno prohlížeče.

1. Po úspěšném ověření se virtuální počítač se systémem Linux přihlásí a vypíše Vaše předplatná Azure.

1. Nastavte předplatné Azure, které chcete použít pro příkazy Azure CLI.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Vytvořte na virtuálním počítači adresář pro certifikáty.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Stáhněte si certifikáty, které jste uložili v trezoru klíčů: New-Edge-Device-full-Chain. CERT. pem, New-Edge-Device. Key. pem a Azure-IoT-test-Only. root. ca. CERT. pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualizace konfigurace zařízení IoT Edge

Modul runtime IoT Edge používá soubor `/etc/iotedge/config.yaml` k uchování jeho konfigurace. V tomto souboru musíme aktualizovat tři části informací:

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

2. Aktualizujte část certifikáty souboru config. yaml tak, že odeberete úvodní `#` cestu a nastavíte cestu tak, aby soubor vypadal jako v následujícím příkladu:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Ujistěte se, že **certifikáty:** řádek neobsahuje žádné předchozí prázdné znaky a že každý z vnořených certifikátů je odsazen o dva mezery.

    Kliknutím pravým tlačítkem v nano dojde k vložení obsahu schránky do aktuální pozice kurzoru. Pokud chcete řetězec nahradit, pomocí šipek klávesnice přejděte k řetězci, který chcete nahradit, odstraňte řetězec a pak klikněte pravým tlačítkem na vložení z vyrovnávací paměti.

3. V Azure Portal přejděte na svůj virtuální počítač. Zkopírujte název DNS (plně kvalifikovaný název domény počítače) z části **Přehled** .

4. Vložte plně kvalifikovaný název domény do oddílu hostname v souboru config. yml. Ujistěte se, že název je malý.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Uložte a zavřete soubor ( `Ctrl + X` , `Y` , `Enter` ).

6. Restartujte démon iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Ověřte stav procesu démona IoT Edge (za příkazem zadejte ": q", který chcete ukončit).

    ```bash
    systemctl status iotedge
    ```

8. Pokud se zobrazí chyby (barevný text s předponou " \[ Error \] ") v protokolech démona stavu prozkoumávat stav, kde najdete podrobné informace o chybě.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz je součástí sady, kde každý článek sestaví na práci, která se provádí v předchozích verzích. Počkejte prosím na vyčištění všech prostředků, dokud nedokončíte finální kurz.

## <a name="next-steps"></a>Další kroky

Právě jsme dokončili konfiguraci virtuálního počítače Azure jako Azure IoT Edge transparentní brány. Začali jsme vygenerováním testovacích certifikátů, které jsme nahráli do Azure Key Vault. Dále jsme použili skript a šablonu Správce prostředků k nasazení virtuálního počítače s imagí "Ubuntu Server 16,04 LTS Azure IoT Edge + runtime" z Azure Marketplace. Když je virtuální počítač spuštěný a spuštěný přes SSH, přihlásili jsme se k Azure a stáhli jsme certifikáty z Key Vault. Provedli jsme několik aktualizací konfigurace modulu runtime IoT Edge aktualizací souboru config. yaml.

Pokračujte dalším článkem a Sestavujte IoT Edge moduly.

> [!div class="nextstepaction"]
> [Vytvoření a nasazení vlastních modulů IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)
