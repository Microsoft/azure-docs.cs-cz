---
title: Nainstalovat Azure IoT Edge pro Linux ve Windows | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge na zařízeních s Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: ffd3d5c09fe19482a69af2309d8e8d574689e712
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199669"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Instalace a zřízení Azure IoT Edge pro Linux na zařízení s Windows (Preview)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge modul runtime je tím, že zařízení přepíná do IoT Edge zařízení. Modul runtime se dá nasadit na zařízeních z třídy počítače na průmyslové servery. Jakmile v zařízení nakonfigurujete modul runtime IoT Edge, můžete do něj z cloudu začít nasazovat obchodní logiku. Další informace najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

Azure IoT Edge pro Linux ve Windows umožňuje používat Azure IoT Edge na zařízeních s Windows pomocí virtuálních počítačů se systémem Linux. Verze systému Linux Azure IoT Edge a jakékoli nasazené moduly pro Linux běží na virtuálním počítači. Odtud můžou aplikace a kód Windows a modul runtime IoT Edge a moduly volně spolupracovat.

V tomto článku jsou uvedené kroky pro nastavení IoT Edge na zařízení s Windows. Tyto kroky nasadí virtuální počítač se systémem Linux, který obsahuje modul runtime IoT Edge, který se má spustit na zařízení s Windows, a potom zařízení zřídí s identitou IoT Hub zařízení.

>[!NOTE]
>IoT Edge pro Linux ve Windows je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>I když IoT Edge pro Linux ve Windows je Doporučené prostředí pro použití Azure IoT Edge v prostředí Windows, kontejnery Windows jsou stále k dispozici. Pokud dáváte přednost použití kontejnerů Windows, přečtěte si návod k [instalaci a správě Azure IoT Edge pro Windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Požadavky

* Účet Azure s platným předplatným. Pokud ještě nemáte [předplatné Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) v Azure úrovně Free nebo Standard.

* Zařízení s Windows s následujícími minimálními požadavky na systém:

  * Windows 10 verze 1809 nebo novější; Build 17763 nebo novější
  * Edice Professional, Enterprise nebo Server
  * Minimální velikost paměti RAM: 4 GB (doporučeno 8 GB)
  * Minimální úložiště: 10 GB

* Přístup k centru pro správu systému Windows s nainstalovaným rozšířením Azure IoT Edge pro centrum pro správu systému Windows:

   1. Stáhněte si [instalační program centra pro správu systému Windows](https://aka.ms/wacdownload).

   1. Spusťte stažený instalační program a postupujte podle pokynů Průvodce instalací pro instalaci centra pro správu systému Windows. Po instalaci otevřete centrum pro správu systému Windows.

   1. Při prvním použití centra pro správu Windows se zobrazí výzva k výběru certifikátu, který se má použít. Jako certifikát vyberte **klienta centra pro správu systému Windows** .

   1. Instalace rozšíření Azure IoT Edge je v čase. V pravém horním rohu řídicího panelu centra pro správu systému Windows vyberte ikonu ozubeného kolečka.

      ![Vyberte ikonu ozubeného kolečka v pravém horním rohu řídicího panelu pro přístup k nastavení.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. V nabídce **Nastavení** v části **Brána** vyberte **rozšíření**.

   1. Vyberte kartu **informační kanály** a vyberte **Přidat**.

   1. https://aka.ms/wac-insiders-feedDo textového pole zadejte a vyberte **Přidat**.

   1. Po přidání informačního kanálu přejděte na kartu **rozšíření k dispozici** . Aktualizace seznamu rozšíření může chvíli trvat.

   1. Na kartě **rozšíření k dispozici** vyhledejte v seznamu rozšíření **Azure IoT Edge** . Zvolte ho a potom na seznam rozšíření vyberte příkazový řádek **instalace** .

   1. Po dokončení instalace byste měli vidět Azure IoT Edge v seznamu nainstalovaných rozšíření na kartě **nainstalované přípony** .

## <a name="choose-your-provisioning-method"></a>Volba způsobu zřizování

Azure IoT Edge pro Linux v systému Windows podporuje následující metody zřizování:

* Ruční zřizování pomocí připojovacího řetězce zařízení IoT Edge Pokud chcete použít tuto metodu, zaregistrujte své zařízení a načtěte připojovací řetězec pomocí postupu v části [registrace zařízení IoT Edge v IoT Hub](how-to-register-device.md).
  * Vyberte možnost ověřování symetrického klíče, protože certifikáty podepsané svým držitelem (509) nejsou aktuálně podporované IoT Edge pro Linux ve Windows.
* Automatické zřizování pomocí služby Device Provisioning Service (DPS) a symetrických klíčů. Přečtěte si další informace o [vytváření a zřizování zařízení IoT Edge pomocí DPS a symetrických klíčů](how-to-auto-provision-symmetric-keys.md).
* Automatické zřizování pomocí certifikátů DPS a X. 509. Přečtěte si další informace o [Vytvoření a zřízení IoT Edge zařízení s certifikáty DPS a X. 509](how-to-auto-provision-x509-certs.md).

Ruční zřizování je snazší, pokud chcete začít s několika zařízeními. Služba Device Provisioning je užitečná pro zřizování mnoha zařízení.

Pokud plánujete použít jednu z metod DPS ke zřízení zařízení nebo zařízení, postupujte podle kroků uvedených v příslušném článku výše a vytvořte instanci DPS, propojte svou instanci DPS s vaším IoT Hub a vytvořte registraci DPS. Můžete vytvořit *jednotlivou registraci* pro jedno zařízení nebo *skupinu pro zápis skupin* zařízení. Další informace o typech registrace najdete v [konceptech Azure IoT Hub Device Provisioning Service](../iot-dps/concepts-service.md#enrollment).

## <a name="create-a-new-deployment"></a>Vytvořit nové nasazení

Vytvořte nasazení Azure IoT Edge pro Linux ve Windows na cílovém zařízení.

# <a name="windows-admin-center"></a>[Centrum pro správu systému Windows](#tab/windowsadmincenter)

Na úvodní stránce centra pro správu systému Windows pod seznamem připojení se zobrazí připojení k místnímu hostiteli, které představuje počítač, na kterém je spuštěno centrum pro správu systému Windows. Tady se zobrazí i všechny další servery, počítače nebo clustery, které spravujete.

Centrum pro správu systému Windows můžete použít k instalaci a správě Azure IoT Edge pro Linux v systému Windows buď na místním zařízení, nebo na vzdálených spravovaných zařízeních. V této příručce bude připojení k místnímu hostiteli sloužit jako cílové zařízení pro nasazení Azure IoT Edge pro Linux ve Windows.

Pokud chcete místo místního zařízení nasadit na vzdálené cílové zařízení a v seznamu nevidíte požadované cílové zařízení, postupujte podle [pokynů pro přidání zařízení.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Počáteční řídicí panel centra pro správu Windows s uvedeným cílovým zařízením](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Vyberte **Přidat**.

1. V podokně **Přidat nebo vytvořit prostředky** vyhledejte dlaždici **Azure IoT Edge** . Vyberte **vytvořit novou** a nainstalujte novou instanci Azure IoT Edge pro Linux v systému Windows na zařízení.

   Pokud už máte IoT Edge pro Linux ve Windows běžící na zařízení, můžete vybrat **Přidat** a připojit se k existujícímu zařízení IoT Edge a spravovat ho pomocí centra pro správu Windows.

   ![Vyberte možnost vytvořit nový v Azure IoT Edge dlaždici v centru pro správu systému Windows.](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Otevře se okno **vytvořit Azure IoT Edge pro Linux v nasazení systému Windows** . Na **1. Začínáme** kartě ověřte, zda cílové zařízení splňuje minimální požadavky, a vyberte možnost **Další**.

1. Přečtěte si licenční podmínky, **vyberte Souhlasím a** **pak další**.

1. **Volitelná diagnostická data** můžete zapnout nebo vypnout v závislosti na vašich preferencích.

1. Vyberte **Další: nasadit**.

   ![Po přepnutí volitelných diagnostických dat do Předvolby vyberte tlačítko Další: nasadit.](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. Na **2. Karta nasadit** v části **Vyberte cílové zařízení** klikněte na zařízení, které chcete ověřit, aby splňovalo minimální požadavky. Jakmile se stav potvrdí jako podporovaný, vyberte **Další**.

   ![Vyberte zařízení, abyste ověřili, že je podporovaný.](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Přijměte výchozí nastavení na kartě **nastavení 2,2** .

1. Na kartě **nasazení 2,3** můžete sledovat průběh nasazení. Úplný proces zahrnuje stažení Azure IoT Edge pro Linux do balíčku Windows, instalaci balíčku, konfiguraci hostitelského zařízení a nastavení virtuálního počítače se systémem Linux. Dokončení tohoto procesu může trvat několik minut. Úspěšné nasazení je znázorněno níže.

   ![Úspěšné nasazení zobrazí každý krok se zeleným znakem zaškrtnutí a jmenovkou Complete.](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Po dokončení nasazení budete připraveni zřídit své zařízení. Vyberte **Další: připojit** a pokračujte na **3. Karta připojit** , která zpracovává Azure IoT Edge zřizování zařízení.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud jste to ještě neučinili, nainstalujte do svého cílového zařízení IoT Edge pro Linux v systému Windows.

> [!NOTE]
> Následující proces PowerShellu popisuje, jak vytvořit nasazení místního hostitele Azure IoT Edge pro Linux ve Windows. Pokud chcete vytvořit nasazení na vzdáleném cílovém zařízení pomocí PowerShellu, můžete použít [vzdálené prostředí PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) k navázání připojení ke vzdálenému zařízení a spuštění těchto příkazů na tomto zařízení vzdáleně.

1. V relaci PowerShellu se zvýšenými oprávněními spusťte každý z následujících příkazů a stáhněte IoT Edge pro Linux ve Windows.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Nainstalujte IoT Edge pro Linux do Windows na svém zařízení.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > Můžete zadat vlastní IoT Edge pro Linux na instalaci systému Windows a adresáře VHDX přidáním parametrů INSTALLDIR = "<FULLY_QUALIFIED_PATH>" a VHDXDIR = "<FULLY_QUALIFIED_PATH>" do příkazu install výše.

1. Aby nasazení bylo úspěšně spuštěno, je třeba nastavit zásady spouštění na cílovém zařízení, pokud ještě není `AllSigned` . Aktuální zásady spouštění můžete na příkazovém řádku PowerShellu se zvýšenými oprávněními ověřit pomocí:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Pokud zásada spouštění není `local machine` `AllSigned` , můžete nastavit zásady spouštění pomocí:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Vytvořte IoT Edge pro Linux v nasazení Windows.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > Tento příkaz můžete spustit bez parametrů nebo volitelně přizpůsobit nasazení pomocí parametrů. Jejich význam najdete v [referenčních informacích ke skriptům IoT Edge pro Linux v prostředí Windows PowerShell](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) .

1. Pokud souhlasíte s licenčními podmínkami, zadejte ' Y '.

1. Pokud chcete zapnout nebo vypnout **volitelná diagnostická data** , zadejte "O" nebo "R", v závislosti na vaší předvolbách. Úspěšné nasazení je znázorněno níže.

   ![Po úspěšném nasazení se na konci zpráv říká úspěšné nasazení.](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Po dokončení nasazení budete připraveni zřídit své zařízení.

---

Pokud chcete zřídit své zařízení, můžete postupovat podle odkazů níže a přejít do části pro vybranou metodu zřizování:

* [Možnost 1: ruční zřizování pomocí připojovacího řetězce zařízení IoT Edge](#option-1-provisioning-manually-using-the-connection-string)
* [Možnost 2: Automatické zřizování pomocí služby Device Provisioning Service (DPS) a symetrických klíčů](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Možnost 3: Automatické zřizování pomocí certifikátů DPS a X. 509](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Zřízení zařízení

Vyberte metodu zřízení zařízení a postupujte podle pokynů v příslušné části. K zřizování zařízení můžete použít Centrum pro správu Windows nebo relaci PowerShellu se zvýšenými oprávněními.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Možnost 1: ruční zřízení pomocí připojovacího řetězce

Tato část se zabývá ručním zřizováním zařízení pomocí připojovacího řetězce zařízení Azure IoT Edge.

# <a name="windows-admin-center"></a>[Centrum pro správu systému Windows](#tab/windowsadmincenter)

1. V podokně **Azure IoT Edge zřizování zařízení** vyberte **připojovací řetězec (ruční)** z rozevíracího seznamu metoda zřizování.

1. V [Azure Portal](https://ms.portal.azure.com/)přejděte na kartu **IoT Edge** IoT Hub.

1. Klikněte na ID zařízení zařízení. Zkopírujte pole **primární připojovací řetězec** .

1. Vložte jej do pole Připojovací řetězec zařízení v centru pro správu systému Windows. Pak zvolte **zřizování s vybranou metodou**.

   ![Po vložení připojovacího řetězce zařízení zvolit zřizování s vybranou metodou](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Po dokončení zřizování vyberte **Dokončit**. Vrátíte se zpátky na hlavní řídicí panel. Nyní by se mělo zobrazit nové zařízení v seznamu, jehož typ je `IoT Edge Devices` . Můžete vybrat zařízení IoT Edge, ke kterému se chcete připojit. Po zobrazení stránky s **přehledem** můžete zobrazit **seznam IoT Edge modulu** a **IoT Edge stav** zařízení.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. V [Azure Portal](https://ms.portal.azure.com/)přejděte na kartu **IoT Edge** IoT Hub.

1. Klikněte na ID zařízení zařízení. Zkopírujte pole **primární připojovací řetězec** .

1. Vložte zástupný text do následujícího příkazu a spusťte ho v relaci PowerShellu se zvýšenými oprávněními na cílovém zařízení.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Možnost 2: zřizování prostřednictvím DPS pomocí symetrických klíčů

V této části se dozvíte, jak automaticky zřídit zařízení pomocí DPS a symetrických klíčů.

# <a name="windows-admin-center"></a>[Centrum pro správu systému Windows](#tab/windowsadmincenter)

1. V podokně **Azure IoT Edge zřizování zařízení** vyberte v rozevíracím seznamu metoda zřizování možnost **symetrický klíč (DPS)** .

1. V [Azure Portal](https://ms.portal.azure.com/)přejděte na instanci DPS.

1. Na kartě **Přehled** Zkopírujte hodnotu **Rozsah ID** . Vložte jej do pole ID oboru v centru pro správu systému Windows.

1. Na kartě **spravovat registrace** v Azure Portal vyberte registraci, kterou jste vytvořili. Zkopírujte hodnotu **primárního klíče** v podrobnostech o registraci. Vložte jej do pole symetrický klíč v centru pro správu systému Windows.

1. V poli ID registrace v centru pro správu systému Windows zadejte ID registrace vašeho zařízení.

1. Vyberte **zřizování s vybranou metodou**.

   ![Po vyplnění požadovaných polí pro zajišťování symetrických klíčů vyberte zřizování s vybranou metodou.](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Po dokončení zřizování vyberte **Dokončit**. Vrátíte se zpátky na hlavní řídicí panel. Nyní by se mělo zobrazit nové zařízení v seznamu, jehož typ je `IoT Edge Devices` . Můžete vybrat zařízení IoT Edge, ke kterému se chcete připojit. Po zobrazení stránky s **přehledem** můžete zobrazit **seznam IoT Edge modulu** a **IoT Edge stav** zařízení.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Zkopírujte do textového editoru následující příkaz. Zástupný text nahraďte podrobnějšími informacemi.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. V [Azure Portal](https://ms.portal.azure.com/)přejděte na instanci DPS.

1. Na kartě **Přehled** Zkopírujte hodnotu **Rozsah ID** . Vložte ho přes příslušný zástupný text v příkazu.

1. Na kartě **spravovat registrace** v Azure Portal vyberte registraci, kterou jste vytvořili. Zkopírujte hodnotu **primárního klíče** v podrobnostech o registraci. Vložte ho přes příslušný zástupný text v příkazu.

1. Zadejte ID registrace zařízení, které nahradí příslušný zástupný text v příkazu.

1. Spusťte příkaz v relaci PowerShellu se zvýšenými oprávněními na cílovém zařízení.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Možnost 3: zřizování prostřednictvím DPS pomocí certifikátů X. 509

V této části se dozvíte, jak automaticky zřídit vaše zařízení pomocí certifikátů DPS a X. 509.

# <a name="windows-admin-center"></a>[Centrum pro správu systému Windows](#tab/windowsadmincenter)

1. V podokně **Azure IoT Edge zřizování zařízení** vyberte v rozevíracím seznamu metoda zřizování možnost **certifikát X. 509 (DPS)** .

1. V [Azure Portal](https://ms.portal.azure.com/)přejděte na instanci DPS.

1. Na kartě **Přehled** Zkopírujte hodnotu **Rozsah ID** . Vložte jej do pole ID oboru v centru pro správu systému Windows.

1. V poli ID registrace v centru pro správu systému Windows zadejte ID registrace vašeho zařízení.

1. Nahrajte soubor certifikátu a privátního klíče.

1. Vyberte **zřizování s vybranou metodou**.

   ![Po vyplnění požadovaných polí pro zřizování certifikátů X. 509 vyberte zřizování s vybranou metodou.](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Po dokončení zřizování vyberte **Dokončit**. Vrátíte se zpátky na hlavní řídicí panel. Nyní by se mělo zobrazit nové zařízení v seznamu, jehož typ je `IoT Edge Devices` . Můžete vybrat zařízení IoT Edge, ke kterému se chcete připojit. Po zobrazení stránky s **přehledem** můžete zobrazit **seznam IoT Edge modulu** a **IoT Edge stav** zařízení.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Zkopírujte do textového editoru následující příkaz. Zástupný text nahraďte podrobnějšími informacemi.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. V [Azure Portal](https://ms.portal.azure.com/)přejděte na instanci DPS.

1. Na kartě **Přehled** Zkopírujte hodnotu **Rozsah ID** . Vložte ho přes příslušný zástupný text v příkazu.

1. Zadejte ID registrace zařízení, které nahradí příslušný zástupný text v příkazu.

1. Nahraďte příslušný zástupný text absolutní cestou ke zdroji souboru certifikátu.

1. Nahraďte příslušný zástupný text absolutní cestou ke zdroji souboru vašeho privátního klíče.

1. Spusťte příkaz v relaci PowerShellu se zvýšenými oprávněními na cílovém zařízení.

---

## <a name="verify-successful-configuration"></a>Ověřit úspěšnou konfiguraci

Ověřte, že IoT Edge pro Linux v systému Windows byl úspěšně nainstalován a nakonfigurován v zařízení IoT Edge.

# <a name="windows-admin-center"></a>[Centrum pro správu systému Windows](#tab/windowsadmincenter)

1. Vyberte zařízení IoT Edge ze seznamu připojených zařízení v centru pro správu Windows, abyste se k němu mohli připojit.

1. Na stránce Přehled zařízení se zobrazí některé informace o zařízení:

    1. Část **seznam IoT Edge modulu** zobrazuje spuštěné moduly na zařízení. Když se služba IoT Edge poprvé spustí, měl by se zobrazit jenom modul **edgeAgent** spuštěný. Ve výchozím nastavení se spustí modul edgeAgent a pomůže vám nainstalovat a spustit všechny další moduly, které nasadíte do svého zařízení.
    1. V části **stav IoT Edge** se zobrazuje stav služby a mělo by se jednat o **aktivní (spuštěné)**.

1. Pokud potřebujete řešit potíže se službou IoT Edge, použijte nástroj **příkazového řádku** na stránce zařízení na stránku SSH (Secure Shell) do virtuálního počítače a spusťte příkazy systému Linux.

    1. Pokud potřebujete řešit potíže se službou, načtěte protokoly služby.

       ```bash
       journalctl -u iotedge
       ```

    2. Použijte `check` Nástroj k ověření stavu konfigurace a připojení zařízení.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Přihlaste se ke svému IoT Edge pro Linux na virtuálním počítači s Windows pomocí následujícího příkazu v relaci PowerShellu:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

1. Po přihlášení můžete pomocí následujícího příkazu pro Linux vyhledat seznam spuštěných IoT Edge modulů:

   ```bash
   iotedge list
   ```

1. Pokud potřebujete řešit potíže se službou IoT Edge, použijte následující příkazy systému Linux.

    1. Pokud potřebujete řešit potíže se službou, načtěte protokoly služby.

       ```bash
       journalctl -u iotedge
       ```

    2. Použijte `check` Nástroj k ověření stavu konfigurace a připojení zařízení.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Další kroky

Pokračujte v [nasazení IoT Edge moduly](how-to-deploy-modules-portal.md) a Naučte se, jak nasadit moduly do svého zařízení.
