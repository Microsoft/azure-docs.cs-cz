---
title: 'Kurz: konfigurace Azure IoT Edge zařízení – Machine Learning na IoT Edge'
description: V tomto kurzu nakonfigurujete virtuální počítač Azure se systémem Linux jako Azure IoT Edge zařízení, které funguje jako transparentní brána.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: b59f8343c9dff07a32accd471f70ddf9f5309b8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463081"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Kurz: konfigurace zařízení Azure IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

V tomto článku nakonfigurujeme virtuální počítač Azure se systémem Linux, aby se jednalo o Azure IoT Edge zařízení, které funguje jako transparentní brána. Transparentní konfigurace brány umožňuje zařízením připojit se k Azure IoT Hub prostřednictvím brány bez vědomí, že brána existuje. Zároveň uživatel, který pracuje se zařízeními v IoT Hub, neví o zařízení zprostředkující brány. Nakonec přidáme do našeho systému Edge Analytics tak, že do transparentní brány přidáte IoT Edge moduly.

>[!NOTE]
>Koncepty v tomto kurzu se vztahují na všechny verze IoT Edge, ale ukázkové zařízení, které vytvoříte k vyzkoušení scénáře, se spouští IoT Edge verze 1,1.

Kroky v tomto článku jsou obvykle prováděny vývojářem cloudu.

V této části kurzu se dozvíte, jak:

> [!div class="checklist"]
>
> * Vytvořte certifikáty, aby bylo možné zařízení brány bezpečně připojit k zařízením pro příjem dat.
> * Vytvořte zařízení IoT Edge.
> * Vytvořte virtuální počítač Azure pro simulaci zařízení IoT Edge.

## <a name="prerequisites"></a>Předpoklady

Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Každý článek v sérii vychází z práce v předchozím článku. Pokud jste dorazili přímo do tohoto článku, přečtěte si [první článek](tutorial-machine-learning-edge-01-intro.md) v řadě.

## <a name="create-certificates"></a>Vytvoření certifikátů

Aby zařízení fungovalo jako brána, musí se bezpečně připojit k zařízením pro příjem dat. Pomocí IoT Edge můžete k nastavení zabezpečených připojení mezi zařízeními použít infrastrukturu veřejných klíčů (PKI). V tomto případě umožníme zařízení IoT pro příjem připojení k IoT Edgemu zařízení, které funguje jako transparentní brána. Aby bylo možné zajistit přiměřené zabezpečení, musí zařízení pro příjem dat potvrdit identitu zařízení IoT Edge. Další informace o tom, jak IoT Edge zařízení používají certifikáty, najdete v tématu [Azure IoT Edge informace o využití certifikátu](iot-edge-certs.md).

V této části vytvoříme certifikáty podepsané svým držitelem pomocí Image Docker, kterou následně sestavíme a spustíte. Rozhodli jste se použít k dokončení tohoto kroku image Docker, protože se sníží počet kroků potřebných k vytvoření certifikátů na vývojovém počítači s Windows. Informace o tom, co máme automatizovaně s imagí Docker, najdete v tématu věnovaném [Vytvoření ukázkových certifikátů pro otestování IoT Edgech funkcí zařízení](how-to-create-test-certificates.md).

1. Přihlaste se ke svému vývojářskému VIRTUÁLNÍmu počítači.
1. Vytvořte novou složku s cestou a názvem **c:\edgeCertificates**.

1. Pokud ještě není spuštěný, spusťte **Docker for Windows** v nabídce Windows Start.

1. Otevřete Visual Studio Code.

1. Vyberte **soubor**  >  **Otevřít složku** a pak vyberte **C: \\ source \\ IoTEdgeAndMlSample \\ CreateCertificates**.

1. V podokně **Průzkumník** klikněte pravým tlačítkem na **souboru Dockerfile** a vyberte **sestavit image**.

1. V dialogovém okně přijměte výchozí hodnotu pro název obrázku a značku: **createcertificates: nejnovější**.

    ![Snímek obrazovky, který ukazuje vytváření certifikátů v Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Počkejte na dokončení sestavení.

    > [!NOTE]
    > Může se zobrazit upozornění týkající se chybějícího veřejného klíče. Toto upozornění je bezpečné ignorovat. Podobně se zobrazí upozornění zabezpečení, které vám doporučí kontrolu nebo resetování oprávnění k imagi, což je bezpečné pro tuto bitovou kopii ignorovat.

1. V okně Visual Studio Code terminálu spusťte kontejner createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker vás vyzve pro přístup k jednotce **c: \\** . Vyberte možnost **sdílet**.

1. Po zobrazení výzvy zadejte své přihlašovací údaje.

1. Po dokončení kontejneru vyhledejte v části **c: \\ edgeCertificates** následující soubory:

    * c: \\ edgeCertificates \\ certifikáty \\ Azure-IoT-test-Only. root. ca. CERT. pem
    * c: \\ edgeCertificates \\ certifikáty \\ New-Edge-Device-full-Chain. CERT. pem
    * c: \\ edgeCertificates \\ certifikáty \\ New-Edge-Device. CERT. pem
    * c: \\ edgeCertificates \\ certifikáty \\ New-Edge-Device. CERT. pfx
    * c: \\ edgeCertificates \\ Private \\ New-Edge-Device. Key. pem

## <a name="upload-certificates-to-azure-key-vault"></a>Nahrání certifikátů do Azure Key Vault

K bezpečnému uložení certifikátů a jejich zpřístupnění z více zařízení nahrajeme certifikáty do Azure Key Vault. Jak vidíte v předchozím seznamu, máme dva typy souborů certifikátu: PFX a PEM. Soubor PFX budeme považovat za Key Vault certifikáty, které se mají nahrát do Key Vault. Soubory PEM jsou prostého textu a budeme je považovat za Key Vault tajných klíčů. Použijeme instanci Key Vault přidruženou k pracovnímu prostoru Azure Machine Learning, který jsme vytvořili spuštěním [poznámkových bloků Jupyter](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks).

1. V [Azure Portal](https://portal.azure.com)přejdete do pracovního prostoru Azure Machine Learning.

1. Na stránce Přehled v pracovním prostoru Machine Learning vyhledejte název **Key Vault**.

    ![Snímek obrazovky, který ukazuje kopírování názvu trezoru klíčů.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Na svém vývojovém počítači nahrajte certifikáty do Key Vault. Nahraďte **\<subscriptionId\>** a **\<keyvaultname\>** informacemi o prostředcích.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Pokud budete vyzváni, přihlaste se k Azure.

1. Skript se spustí po dobu několika minut s výstupem, ve kterém se zobrazí nové položky Key Vault.

    ![Snímek obrazovky, který zobrazuje výstup skriptu Key Vault.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>Vytvoříte zařízení IoT Edge.

Pokud chcete zařízení Azure IoT Edge připojit ke službě IoT Hub, nejdřív v centru vytvoříme identitu pro zařízení. Připojovací řetězec přijímáme z identity zařízení v cloudu a používá ho ke konfiguraci modulu runtime na našem IoT Edgem zařízení. Po připojení nakonfigurovaného zařízení k centru můžeme nasadit moduly a odesílat zprávy. Konfiguraci fyzického IoT Edgeho zařízení můžeme změnit také tak, že v IoT Hub změníte jeho odpovídající identitu zařízení.

V tomto kurzu vytvoříme novou identitu zařízení pomocí Visual Studio Code. Tyto kroky můžete provést také pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI.

1. Na svém vývojovém počítači otevřete Visual Studio Code.

1. Rozbalte rámec **Azure IoT Hub** v zobrazení **Průzkumník** Visual Studio Code.

1. Vyberte tři tečky a vyberte **vytvořit IoT Edge zařízení**.

1. Dejte zařízení název. Pro usnadnění práce používáme název **aaTurbofanEdgeDevice** , aby se vyřadí na začátek seznamu zařízení.

1. Nové zařízení se zobrazí v seznamu zařízení.

    ![Snímek obrazovky, který zobrazuje zobrazení zařízení v Průzkumníkovi Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Nasazení virtuálního počítače Azure

K vytvoření našeho IoT Edge zařízení pro tento kurz používáme image [Azure IoT Edge na Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) z Azure Marketplace. Azure IoT Edge image Ubuntu nainstaluje nejnovější modul runtime IoT Edge a jeho závislosti při spuštění. Virtuální počítač nasadíme pomocí:

- Skript PowerShellu, `Create-EdgeVM.ps1` .
- Šablona Azure Resource Manager, `IoTEdgeVMTemplate.json` .
- Skript prostředí, `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Povolit programové nasazení

Pokud chcete použít image z Azure Marketplace ve skriptovém nasazení, musíme pro Image povolit programové nasazení.

1. Přihlaste se k webu Azure Portal.

1. Vyberte **Všechny služby**.

1. Na panelu hledání zadejte a vyberte **Marketplace**.

1. Na panelu hledání na webu Marketplace zadejte a vyberte **Azure IoT Edge v Ubuntu**.

1. Vyberte **hypertextový odkaz Začínáme,** který chcete nasadit programově.

1. Vyberte tlačítko **Povolit** a pak vyberte **Uložit**.

    ![Snímek obrazovky, který ukazuje povolení programového nasazení pro virtuální počítač.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Zobrazí se oznámení o úspěchu.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Potom spuštěním skriptu vytvořte virtuální počítač pro zařízení IoT Edge.

1. Otevřete okno PowerShellu a přejdete do adresáře **EdgeVM** .

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Spusťte skript pro vytvoření virtuálního počítače.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Až budete vyzváni, zadejte hodnoty pro každý parametr. Pro předplatné, skupinu prostředků a umístění doporučujeme použít stejné hodnoty jako u všech prostředků v rámci tohoto kurzu.

    * **ID předplatného Azure**: našlo se v Azure Portal.
    * **Název skupiny prostředků**: zapamatovatelné názvy pro seskupení prostředků pro tento kurz.
    * **Umístění**: umístění Azure, ve kterém se virtuální počítač vytvoří. Například westus2 nebo northeurope. Další informace najdete v tématu všechna [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: název účtu správce, který použijete pro přihlášení k virtuálnímu počítači.
    * **AdminPassword**: heslo, které se má nastavit pro uživatelské jméno správce na virtuálním počítači.

1. Aby se skript nastavil jako virtuální počítač, přihlaste se k Azure pomocí přihlašovacích údajů přidružených k předplatnému Azure, které používáte.

1. Skript potvrdí informace pro vytvoření virtuálního počítače. Pokračujte výběrem **y** nebo **ENTER** .

1. Skript se spustí několik minut, protože provede následující kroky:

    * Vytvoří skupinu prostředků, pokud už neexistuje.
    * Vytvoří virtuální počítač.
    * Přidá výjimky NSG pro virtuální počítač pro porty 22 (SSH), 5671 (AMQP), 5672 (AMPQ) a 443 (TLS).
    * Nainstaluje [Azure CLI](/cli/azure/install-azure-cli-apt) .

1. Skript vytvoří výstup připojovacího řetězce SSH pro připojení k virtuálnímu počítači. Zkopírujte připojovací řetězec pro další krok.

    ![Snímek obrazovky, který ukazuje kopírování připojovacího řetězce SSH pro virtuální počítač.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Připojení k zařízení IoT Edge

V následujících částech se konfiguruje virtuální počítač Azure, který jsme vytvořili. Prvním krokem je připojení k virtuálnímu počítači.

1. Otevřete příkazový řádek a vložte připojovací řetězec SSH, který jste zkopírovali z výstupu skriptu. Zadejte vlastní informace pro uživatelské jméno, příponu a oblast podle hodnot, které jste zadali do skriptu PowerShellu v předchozí části.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Až budete vyzváni k ověření pravosti hostitele, zadejte **Ano** a vyberte **ENTER**.

1. Po zobrazení výzvy zadejte heslo.

1. Ubuntu zobrazí uvítací zprávu a pak by se měla zobrazit výzva jako `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Stažení Key Vault certifikátů

Dříve v tomto článku jsme nahráli certifikáty, které Key Vault, aby byly k dispozici pro naše IoT Edge zařízení a naše listová zařízení. Listové zařízení je zařízení, které používá IoT Edge zařízení jako brána ke komunikaci s IoT Hub.

Později v tomto kurzu budeme pracovat se listovým zařízením. V této části si stáhněte certifikáty do zařízení IoT Edge.

1. Z relace SSH na virtuálním počítači se systémem Linux se přihlaste k Azure pomocí Azure CLI.

    ```azurecli
    az login
    ```

1. Zobrazí se výzva k otevření prohlížeče na [přihlašovací stránce zařízení Microsoft](https://microsoft.com/devicelogin) a zadání jedinečného kódu. Tyto kroky můžete provést na svém místním počítači. Až budete s ověřováním hotovi, zavřete okno prohlížeče.

1. Po úspěšném ověření se virtuální počítač se systémem Linux přihlásí a vypíše Vaše předplatná Azure.

1. Nastavte předplatné Azure, které chcete použít pro příkazy Azure CLI.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Vytvořte na virtuálním počítači adresář pro certifikáty.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Stáhněte si certifikáty, které jste uložili v trezoru klíčů: New-Edge-Device-full-Chain. CERT. pem, New-Edge-Device. Key. pem a Azure-IoT-test-Only. root. ca. CERT. pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualizace konfigurace zařízení IoT Edge

Modul runtime IoT Edge používá soubor/etc/iotedge/config.yaml k uchování jeho konfigurace. V tomto souboru musíme aktualizovat tři části informací:

* **Připojovací řetězec zařízení**: připojovací řetězec z identity tohoto zařízení v IoT Hub
* **Certifikáty**: certifikáty, které se mají použít pro připojení s podřízenými zařízeními.
* **Název hostitele**: plně kvalifikovaný název domény (FQDN) virtuálního počítače IoT Edge zařízení

Azure IoT Edge image Ubuntu, kterou jsme použili k vytvoření virtuálního počítače s IoT Edge, přichází pomocí skriptu prostředí, který aktualizuje soubor config. yaml připojovacím řetězcem.

1. V Visual Studio Code klikněte pravým tlačítkem myši na IoT Edge zařízení a pak vyberte **Kopírovat připojovací řetězec zařízení**.

    ![Snímek obrazovky, který ukazuje zkopírování připojovacího řetězce z Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. V relaci SSH spuštěním příkazu aktualizujte soubor config. yaml pomocí připojovacího řetězce zařízení.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

V dalším kroku aktualizujeme certifikáty a název hostitele přímým úpravou souboru config. yaml.

1. Otevřete soubor config. yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Aktualizujte část certifikáty souboru config. yaml tak, že odeberete úvodní **#** cestu a nastavíte cestu tak, aby soubor vypadal jako v následujícím příkladu:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Ujistěte se, že **certifikáty:** řádek neobsahuje žádné předchozí prázdné znaky a že každý z vnořených certifikátů je odsazen o dva mezery.

    Kliknutím pravým tlačítkem v nano dojde k vložení obsahu schránky do aktuální pozice kurzoru. Chcete-li řetězec nahradit, přejděte k řetězci, který chcete nahradit, pomocí šipek klávesnice, odstraňte řetězec a potom klikněte pravým tlačítkem na vložení z vyrovnávací paměti.

1. V Azure Portal přejdete na svůj virtuální počítač. Zkopírujte název DNS (plně kvalifikovaný název domény počítače) z části **Přehled** .

1. Vložte plně kvalifikovaný název domény do oddílu hostname v souboru config. yml. Ujistěte se, že je název malý.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Soubor uložte a zavřete výběrem **kombinace kláves CTRL + X**, **Y** a **ENTER**.

1. Restartujte proces IoT Edge démona.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Ověřte stav procesu démona IoT Edge. Po příkazu zadejte **: q** pro ukončení.

    ```bash
    systemctl status iotedge
    ```

1. Pokud se zobrazí chyby (barevný text s \[ chybou "Chyba \] ") ve stavu, zkontrolujte protokoly démona, kde najdete podrobné informace o chybě.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz je součástí sady, kde každý článek sestaví na práci, která se provádí v předchozích verzích. Počkejte na vyčištění všech prostředků, dokud nedokončíte finální kurz.

## <a name="next-steps"></a>Další kroky

Právě jsme dokončili konfiguraci virtuálního počítače Azure jako IoT Edge transparentní brány. Začali jsme vygenerováním testovacích certifikátů, které jsme nahráli do Key Vault. V dalším kroku jsme použili skript a šablonu Správce prostředků k nasazení virtuálního počítače s imagí Ubuntu serveru 16,04 LTS + Azure IoT Edge runtime z Azure Marketplace. Když je virtuální počítač spuštěný a spuštěný, připojili jsme se přes SSH. Přihlásili jsme se k Azure a stáhli jsme certifikáty z Key Vault. Provedli jsme několik aktualizací konfigurace modulu runtime IoT Edge aktualizací souboru config. yaml.

Pokračujte dalším článkem a Sestavujte IoT Edge moduly.

> [!div class="nextstepaction"]
> [Vytvoření a nasazení vlastních modulů IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)
