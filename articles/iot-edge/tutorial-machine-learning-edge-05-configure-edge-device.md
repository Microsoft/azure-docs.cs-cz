---
title: 'Kurz: Konfigurace zařízení IoT Edge – Machine Learning na Azure IoT Edge'
description: V tomto kurzu nakonfigurujete virtuální počítač Azure se systémem Linux jako zařízení Azure IoT Edge, které funguje jako transparentní brána.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d2bad581c925bb62cbe65a45000f6d3ae35db011
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372689"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Kurz: Konfigurace zařízení IoT Edge

> [!NOTE]
> Tento článek je součástí řady pro kurz o používání Azure Machine Learning na IoT Edge. Pokud jste k tomuto článku dorazili přímo, doporučujeme vám začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) v sérii pro dosažení nejlepších výsledků.

V tomto článku nakonfigurujeme virtuální počítač Azure se systémem Linux jako zařízení IoT Edge, které funguje jako transparentní brána. Transparentní konfigurace brány umožňuje zařízením připojit se k Azure IoT Hub přes bránu bez vědomí, že brána existuje. Současně uživatel interakci se zařízeními v Azure IoT Hub neví o zařízení zprostředkující brány. Nakonec přidáme do našeho systému edge analytics přidáním modulů IoT Edge do transparentní brány.

Kroky v tomto článku jsou obvykle prováděny vývojáře cloudu.

## <a name="create-certificates"></a>Vytvoření certifikátů

Aby zařízení fungovalo jako brána, musí být schopno bezpečně se připojit k zařízením pro příjem dat. Azure IoT Edge umožňuje používat infrastrukturu veřejných klíčů (PKI) k nastavení zabezpečeného připojení mezi zařízeními. V takovém případě povolujeme zařízení IoT pro připojení k zařízení IoT Edge, které funguje jako transparentní brána. Chcete-li zachovat přiměřené zabezpečení, následné zařízení by mělo potvrdit identitu zařízení IoT Edge. Další informace o tom, jak zařízení IoT Edge používají certifikáty, najdete [v tématu Podrobnosti o využití certifikátu Azure IoT Edge](iot-edge-certs.md).

V této části vytvoříme certifikáty podepsané svým držitelem pomocí image Dockeru, kterou pak vytvoříme a spustíme. K dokončení tohoto kroku jsme se rozhodli použít bitovou kopii Dockeru, protože výrazně snižuje počet kroků potřebných k vytvoření certifikátů ve vývojovém počítači windows. Viz [Vytvoření ukázkových certifikátů pro testování funkcí zařízení IoT Edge,](how-to-create-test-certificates.md) abyste pochopili, co jsme automatizovali s image Dockeru.

1. Přihlaste se k vývojovému virtuálnímu počítači.

2. Vytvořte novou složku s `c:\edgeCertificates`cestou a názvem .

3. Pokud již není spuštěno, spusťte **Docker pro Windows** z nabídky Start systému Windows.

4. Otevřete Visual Studio Code.

5. Vyberte**Otevřít složku souboru...** **File** > a zvolte **C:\\zdroj\\IoTEdgeAndMlSample\\CreateCertificates**.

6. V podokně Průzkumník klikněte pravým tlačítkem myši na **soubor dockerfile** a zvolte **Vytvořit obraz**.

7. V dialogovém okně přijměte výchozí hodnotu názvu obrázku a **značky: createcertificates: latest**.

    ![Vytvoření certifikátů v kódu sady Visual Studio](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Počkejte na dokončení sestavení.

    > [!NOTE]
    > Může se zobrazit upozornění na chybějící veřejný klíč. Toto upozornění je bezpečné ignorovat. Podobně se zobrazí upozornění zabezpečení, které doporučuje zkontrolovat nebo obnovit oprávnění k bitové kopii, což je pro tento obrázek bezpečné ignorovat.

9. V terminálu visual studio kód okna, spusťte kontejner createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker vyzve k přístupu k **jednotce c:.\\ ** Vyberte **Sdílet**.

11. Po zobrazení výzvy zadejte pověření.

12. Po dokončení spuštění kontejneru zkontrolujte následující soubory v **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\\\edgeCertificates\\private new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Nahrání certifikátů do úložiště klíčů Azure

Chcete-li naše certifikáty bezpečně ukládat a zpřístupnit je z více zařízení, nahrajeme certifikáty do služby Azure Key Vault. Jak můžete vidět z výše uvedeného seznamu, máme dva typy souborů certifikátů: PFX a PEM. PFX budeme považovat pfx jako certifikáty trezoru klíčů, které mají být nahrány do trezoru klíčů. PEM soubory jsou prostý text a budeme s nimi zacházet jako Key Vault tajemství. Použijeme trezor klíčů přidružený k pracovnímu prostoru Azure Machine Learning, který jsme vytvořili spuštěním [poznámkových bloků Azure](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Z [portálu Azure](https://portal.azure.com)přejděte do pracovního prostoru Azure Machine Learning.

2. Na stránce s přehledem pracovního prostoru Azure Machine Learning najdete název **trezoru klíčů**.

    ![Kopírovat název trezoru klíčů](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Ve vývojovém počítači nahrajte certifikáty do trezoru klíčů. Nahraďte ** \<\> subscriptionId** a ** \<keyvaultname\> ** informacemi o prostředkůch.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Pokud se zobrazí výzva, přihlaste se do Azure.

5. Skript bude spuštěn několik minut s výstupem, který obsahuje seznam nových položek trezoru klíčů.

    ![Výstup skriptu trezoru klíčů](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Vytvoření zařízení IoT Edge

Chcete-li připojit zařízení Azure IoT Edge k centru IoT, nejprve vytvoříme identitu pro zařízení v centru. Připojovací řetězec vezmeme z identity zařízení v cloudu a použijeme ho ke konfiguraci runtime na našem zařízení IoT Edge. Jakmile se nakonfigurované zařízení připojí k rozbočovači, budeme schopni nasadit moduly a odesílat zprávy. Můžete také změnit konfiguraci fyzického zařízení IoT Edge změnou jeho odpovídající identity zařízení v centru IoT.

V tomto kurzu vytvoříme novou identitu zařízení pomocí kódu sady Visual Studio. Tyto kroky můžete také provést pomocí [portálu Azure nebo](how-to-register-device.md#register-in-the-azure-portal) [Azure CLI](how-to-register-device.md#register-with-the-azure-cli).

1. Ve vývojovém počítači otevřete visual studio kód.

2. Rozbalte rámec **služby Azure IoT Hub** ze zobrazení Průzkumníka kódu Visual Studia.

3. Klikněte na tři tečky a vyberte **Vytvořit zařízení IoT Edge**.

4. Pojmenujte zařízení. Pro pohodlí používáme název **aaTurbofanEdgeDevice** tak, aby se třídí na vrchol uvedených zařízení.

5. Nové zařízení se zobrazí v seznamu zařízení.

    ![Zobrazit nové zařízení aaTurbofanEdgeDevice v průzkumníku kódu VS](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Nasazení virtuálního počítače Azure

K vytvoření našeho zařízení IoT Edge pro tento kurz používáme image [Azure IoT Edge na Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) na Webu Azure Marketplace. Image Azure IoT Edge na Ubuntu nainstaluje nejnovější runtime Azure IoT Edge a jeho závislosti při spuštění. Virtuální ho nasadíme pomocí `Create-EdgeVM.ps1`skriptu PowerShellu; šablona Správce `IoTEdgeVMTemplate.json`zdrojů ; a shellskript, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Povolení programového nasazení

Chcete-li použít bitovou kopii z marketplace ve skriptovaném nasazení, musíme povolit programové nasazení pro bitovou kopii.

1. Přihlaste se k portálu Azure.

1. Vyberte **Všechny služby**.

1. Na panelu hledání zadejte a vyberte **Marketplace**.

1. Na panelu vyhledávání Marketplace zadejte a vyberte **Azure IoT Edge na Ubuntu**.

1. Vyberte hypertextový odkaz **Začínáme,** který chcete nasadit programově.

1. Vyberte tlačítko **Povolit** a pak **Uložit**.

    ![Povolení programového nasazení pro virtuální počítače](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Zobrazí se oznámení o úspěchu.

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Dále spusťte skript a vytvořte virtuální počítač pro vaše zařízení IoT Edge.

1. Otevřete okno PowerShellu a přejděte do adresáře **EdgeVM.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Spusťte skript a vytvořte virtuální počítač.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Po zobrazení výzvy zadejte hodnoty pro každý parametr. Pro předplatné, skupinu prostředků a umístění doporučujeme použít stejné jako máte pro všechny prostředky v průběhu tohoto kurzu.

    * **ID předplatného Azure:** nalezené na webu Azure Portal
    * **Název skupiny prostředků**: zapamatovatelný název pro seskupení prostředků pro tento kurz
    * **Umístění:** Umístění Azure, kde se vytvoří virtuální počítač. Například westus2 nebo northeurope. Další informace najdete ve všech [umístěních Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: název účtu správce, který budete používat pro přihlášení k virtuálnímu počítači
    * **AdminPassword**: heslo pro nastavení adminusername na virtuálním počítači

4. Aby skript mohl nastavit virtuální počítač, musíte se přihlásit k Azure pomocí přihlašovacích údajů přidružených k předplatnému Azure, které používáte.

5. Skript potvrzuje informace pro vytvoření virtuálního počítače. Chcete-li pokračovat, vyberte **y** nebo **Enter.**

6. Skript se spouští několik minut při provádění následujících kroků:

    * Vytvoření skupiny prostředků, pokud již neexistuje
    * Vytvoření virtuálního počítače
    * Přidání výjimek nsg pro virtuální ho svícen pro porty 22 (SSH), 5671 (AMQP), 5672 (AMPQ) a 443 (TLS)
    * Instalace [příkazového příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. Skript výstupy SSH připojovací řetězec pro připojení k virtuálnímu virtuálnímu zařízení. Zkopírujte připojovací řetězec pro další krok.

    ![Kopírování připojovacího řetězce SSH pro virtuální ms](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Připojení k zařízení IoT Edge

V několika dalších částech nakonfigurujete virtuální počítač Azure, který jsme vytvořili. Prvním krokem je připojení k virtuálnímu počítači.

1. Otevřete příkazový řádek a vložte připojovací řetězec SSH, který jste zkopírovali z výstupu skriptu. Zadejte vlastní informace pro uživatelské jméno, příponu a oblast podle hodnot, které jste zadali skriptu Prostředí PowerShell v předchozí části.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Po zobrazení výzvy k ověření pravosti hostitele zadejte **ano** a vyberte **Enter**.

3. Po zobrazení výzvy zadejte heslo.

4. Ubuntu zobrazí uvítací zprávu a pak byste `<username>@<machinename>:~$`měli vidět výzvu jako .

## <a name="download-key-vault-certificates"></a>Stáhnout certifikáty trezoru klíčů

Dříve v tomto článku jsme nahráli certifikáty do trezoru klíčů, aby byly k dispozici pro naše zařízení IoT Edge a naše listové zařízení. Zařízení leaf je zařízení pro příjem dat, které používá zařízení IoT Edge jako bránu ke komunikaci s službou IoT Hub.

Budeme se zabývat listovým zařízením později v tutoriálu. V této části stáhněte certifikáty do zařízení IoT Edge.

1. Z relace SSH na virtuálním počítači s Linuxem se přihlaste do Azure pomocí azure cli.

    ```azurecli
    az login
    ```

1. Budete vyzváni k otevření prohlížeče <https://microsoft.com/devicelogin> a zadání jedinečného kódu. Tyto kroky můžete provést v místním počítači. Po dokončení ověřování zavřete okno prohlížeče.

1. Když se úspěšně ověříte, virtuální počítač s Linuxem se přihlásí a zobrazí vaše předplatná Azure.

1. Nastavte předplatné Azure, které chcete použít pro příkazy Azure CLI.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Vytvořte adresář na virtuálním počítači pro certifikáty.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Stáhněte si certifikáty, které jste uložili do trezoru klíčů: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem a azure-iot-test-only.root.ca.cert.pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualizace konfigurace zařízení IoT Edge

Runtime IoT Edge používá `/etc/iotedge/config.yaml` soubor k zachování jeho konfigurace. Musíme aktualizovat tři informace v tomto souboru:

* **Připojovací řetězec zařízení:** připojovací řetězec z identity tohoto zařízení v centru IoT Hub
* **Certifikáty:** certifikáty, které se mají použít pro připojení s následnými zařízeními
* **Název hostitele:** plně kvalifikovaný název domény (FQDN) zařízení IoT Edge virtuálního zařízení.

Image *Azure IoT Edge na Ubuntu,* kterou jsme použili k vytvoření virtuálního počítače IoT Edge, je dodáván a shell skript, který aktualizuje config.yaml s připojovacím řetězcem.

1. V kódu Visual Studia klikněte pravým tlačítkem myši na zařízení IoT Edge a pak vyberte **kopírovat připojovací řetězec zařízení**.

    ![Kopírování připojovacího řetězce z kódu sady Visual Studio](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. V relaci SSH spusťte příkaz a aktualizujte soubor config.yaml pomocí připojovacího řetězce zařízení.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Dále budeme aktualizovat certifikáty a název hostitele přímo upravil config.yaml.

1. Otevřete soubor config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Aktualizujte část certifikátů v souboru config.yaml odebráním úvodního `#` a nastavením cesty tak, aby soubor vypadal jako následující příklad:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Ujistěte se, že **certifikáty:** řádek nemá žádné předchozí mezery a že každý z vnořených certifikátů je odsazen dvěma mezerami.

    Kliknutím pravým tlačítkem myši v nano vložíte obsah schránky do aktuální polohy kurzoru. Chcete-li řetězec nahradit, přejděte pomocí šipek klávesnice k přechodu na řetězec, který chcete nahradit, odstraňte řetězec a potom klepnutím pravým tlačítkem myši vložte z vyrovnávací paměti.

3. Na webu Azure Portal přejděte na svůj virtuální počítač. Zkopírujte název DNS (FQDN zařízení) z části **Přehled.**

4. Vložte hlavní název souboru do oddílu název hostitele v souboru config.yml. Ujistěte se, že název je všechna malá písmena.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Uložte a zavřete `Y` `Enter`soubor (`Ctrl + X`, , ).

6. Restartujte daemon a tom je.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Zkontrolujte stav daemonu IoT Edge (za příkazem zadejte ":q" pro ukončení).

    ```bash
    systemctl status iotedge
    ```

8. Pokud se zobrazí chyby (barevný\[\]text s předponou " ERROR ") ve stavu Zkontrolujte protokoly daemonu pro podrobné informace o chybě.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Další kroky

Právě jsme dokončili konfiguraci virtuálního počítače Azure jako transparentní brány Azure IoT Edge. Začali jsme generováním testovacích certifikátů, které jsme nahráli do služby Azure Key Vault. Dále jsme použili skript a šablonu Správce prostředků k nasazení virtuálního počítače s bitovou kopií "Ubuntu Server 16.04 LTS + Azure IoT Edge" z Azure Marketplace. S virtuálním počítačem v provozu jsme připojeni přes SSH, jsme se přihlásili do Azure a stažené certifikáty z Key Vault. Provedli jsme několik aktualizací konfigurace rozhraní IoT Edge Runtime aktualizací souboru config.yaml.

Další informace najdete [v tématu Jak lze zařízení IoT Edge použít jako bránu](iot-edge-as-gateway.md) a [nakonfigurovat zařízení IoT Edge tak, aby působilo jako transparentní brána](how-to-create-transparent-gateway.md).

Pokračujte k dalšímu článku a sestavte moduly IoT Edge.

> [!div class="nextstepaction"]
> [Vytvoření a nasazení vlastních modulů IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)
