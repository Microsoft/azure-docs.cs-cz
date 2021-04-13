---
title: Aktualizovat IoT Edge verzi na zařízeních – Azure IoT Edge | Microsoft Docs
description: Postup aktualizace zařízení IoT Edge pro spuštění nejnovějších verzí démona zabezpečení a modulu runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e5034c228a354c98b5792492d484da9eb10b8cf2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310848"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aktualizace modulu runtime a procesu démon zabezpečení IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Jelikož služba IoT Edge uvolní nové verze, budete chtít aktualizovat IoT Edge zařízení o nejnovější funkce a vylepšení zabezpečení. Tento článek poskytuje informace o tom, jak aktualizovat zařízení IoT Edge, když je dostupná nová verze.

Pokud chcete přejít na novější verzi, je třeba aktualizovat dvě součásti zařízení IoT Edge. První je démon zabezpečení, který běží na zařízení a spouští moduly runtime při spuštění zařízení. V současné době je možné démona zabezpečení aktualizovat pouze ze samotného zařízení. Druhá součást je modul runtime, který se skládá z modulů IoT Edge hub a agentů IoT Edge. V závislosti na způsobu struktury nasazení se dá modul runtime aktualizovat ze zařízení nebo vzdáleně.

Pokud chcete najít nejnovější verzi Azure IoT Edge, přečtěte si téma [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aktualizace démona zabezpečení

Démon zabezpečení IoT Edge je nativní součást, kterou je potřeba aktualizovat pomocí Správce balíčků na zařízení IoT Edge.

Pomocí příkazu ověřte verzi démona zabezpečení spuštěnou na vašem zařízení `iotedge version` .

>[!IMPORTANT]
>Pokud aktualizujete zařízení z verze 1,0 nebo 1,1 na verzi 1,2, existují rozdíly v procesech instalace a konfigurace, které vyžadují další kroky. Další informace najdete v postupu dále v tomto článku: [zvláštní případ: aktualizace z 1,0 nebo 1,1 na 1,2](#special-case-update-from-10-or-11-to-12).

# <a name="linux"></a>[Linux](#tab/linux)

Na zařízeních se systémem Linux x64 pomocí apt-get nebo odpovídajícího správce balíčků aktualizujte démona zabezpečení na nejnovější verzi.

Získat nejnovější konfiguraci úložiště od Microsoftu:

* **Ubuntu Server 18,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Malina Pi OS Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Zkopírujte vygenerovaný seznam.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Nainstalujte veřejný klíč Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Aktualizujte apt.

   ```bash
   sudo apt-get update
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Zkontrolujte, které verze IoT Edge jsou k dispozici.

   ```bash
   apt list -a iotedge
   ```

Pokud chcete aktualizovat na nejnovější verzi démona zabezpečení, použijte následující příkaz, který také aktualizuje **libiothsm-STD** na nejnovější verzi:

   ```bash
   sudo apt-get install iotedge
   ```

Pokud chcete aktualizovat na konkrétní verzi démona zabezpečení, zadejte verzi z výstupu seznamu apt. Vždy, když se **iotedge** aktualizuje, se automaticky pokusí aktualizovat balíček **libiothsm-STD** na jeho nejnovější verzi, což může způsobit konflikt závislosti. Pokud nebudete mít nejnovější verzi, nezapomeňte cílit na oba balíčky na stejnou verzi. Například následující příkaz nainstaluje specifickou verzi 1,1 verze:

   ```bash
   sudo apt-get install iotedge=1.1.1 libiothsm-std=1.1.1
   ```

Pokud verze, kterou chcete nainstalovat, není k dispozici prostřednictvím apt-get, můžete použít kudrlinkou k zacílení libovolné verze z úložiště [IoT Edgech verzí](https://github.com/Azure/azure-iotedge/releases) . Pro každou verzi, kterou chcete nainstalovat, vyhledejte příslušné soubory **libiothsm-STD** a **iotedge** pro vaše zařízení. Pro každý soubor klikněte pravým tlačítkem na odkaz na soubor a zkopírujte adresu odkazu. Pomocí adresy odkaz nainstalujte konkrétní verze těchto součástí:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
```
<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Zkontrolujte, které verze IoT Edge jsou k dispozici.

   ```bash
   apt list -a aziot-edge
   ```

Pokud chcete aktualizovat na nejnovější verzi IoT Edge, použijte následující příkaz, který taky aktualizuje službu identity na nejnovější verzi:

   ```bash
   sudo apt-get install aziot-edge
   ```
<!-- end 1.2 -->
:::moniker-end

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Při IoT Edge pro Linux ve Windows se IoT Edge spouští na virtuálním počítači Linux hostovaném na zařízení s Windows. Tento virtuální počítač je předem nainstalovaný pomocí IoT Edge a je spravovaný pomocí Microsoft Update, aby se komponenty zachovaly v aktuálním stavu. Pokud máte povolené automatické aktualizace, budou se nové aktualizace stahovat a instalovat, jenom když budou k dispozici.

U IoT Edge pro Windows se IoT Edge spouští přímo na zařízení s Windows. Pokyny k aktualizaci pomocí skriptů PowerShellu najdete v tématu [instalace a správa Azure IoT Edge pro Windows](how-to-install-iot-edge-windows-on-windows.md).
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

V současné době není na zařízeních s Windows podporována IoT Edge verze 1,2.

:::moniker-end

---

## <a name="update-the-runtime-containers"></a>Aktualizace kontejnerů modulu runtime

Způsob aktualizace IoT Edge agentů a kontejnerů centra IoT Edge závisí na tom, zda používáte ve svém nasazení valení značky (například 1,1) nebo konkrétní značky (například 1.1.1).

Pomocí příkazů nebo proveďte kontrolu verze IoT Edge agenta a IoT Edge modulů rozbočovačů, které jsou aktuálně na vašem zařízení `iotedge logs edgeAgent` `iotedge logs edgeHub` .

  ![Najít verzi kontejneru v protokolech](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Vysvětlení značek IoT Edge

IoT Edge agent a image centra IoT Edge jsou označené verzí IoT Edge, ke které jsou přidružené. Existují dva různé způsoby použití značek s imagemi za běhu:

* **Válcování značek** – použijte pouze první dvě hodnoty čísla verze, abyste získali nejnovější obrázek, který se shoduje s těmito číslicemi. Například 1,1 se aktualizuje vždy, když je k dispozici nová verze, která odkazuje na nejnovější verzi 1.1. x. Pokud se znovu načte modul runtime kontejneru v zařízení IoT Edge, modul runtime se aktualizuje na nejnovější verzi. Nasazení z Azure Portal výchozí pro vracení značek. *Tento přístup je navržený pro účely vývoje.*

* **Konkrétní značky** – k explicitnímu nastavení verze image použijte všechny tři hodnoty čísla verze. Například 1.1.0 se po počáteční verzi nemění. Až budete připraveni na aktualizaci, můžete deklarovat nové číslo verze v manifestu nasazení. *Tento přístup je navržený pro produkční účely.*

### <a name="update-a-rolling-tag-image"></a>Aktualizace bitové kopie valení značky

Pokud ve svém nasazení používáte přístupné značky (například mcr.microsoft.com/azureiotedge-hub:**1,1**), musíte vynutit modul runtime kontejneru v zařízení, aby využíval nejnovější verzi image.

Odstraňte místní verzi image ze zařízení IoT Edge. V počítačích se systémem Windows odinstalování démona zabezpečení také odstraní image za běhu, takže tento krok nemusíte znovu provádět.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.1
docker rmi mcr.microsoft.com/azureiotedge-agent:1.1
```

K odebrání imagí možná budete muset použít `-f` příznak vynucení.

Služba IoT Edge načte nejnovější verze imagí za běhu a automaticky je znovu spustí na svém zařízení.

### <a name="update-a-specific-tag-image"></a>Aktualizace konkrétního obrázku značky

Pokud ve svém nasazení použijete konkrétní značky (například mcr.microsoft.com/azureiotedge-hub:**1.1.1**), pak vše, co potřebujete, je aktualizovat značku v manifestu nasazení a použít změny na zařízení.

1. V IoT Hub v Azure Portal vyberte zařízení IoT Edge a vyberte možnost **nastavit moduly**.

1. V části **IoT Edge moduly** vyberte **nastavení modulu runtime**.

   ![Konfigurace nastavení modulu runtime](./media/how-to-update-iot-edge/configure-runtime.png)

1. V **nastavení modulu runtime** aktualizujte hodnotu **Image** pro **centrum Edge** s požadovanou verzí. Ještě nevybírejte možnost **Uložit** .

   ![Aktualizovat verzi obrázku centra hran](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Sbalte nastavení **hraničního centra** nebo přejděte dolů a aktualizujte hodnotu **Image** pro **agenta Edge** se stejnou požadovanou verzí.

   ![Aktualizace verze agenta centra hran](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Vyberte **Uložit**.

1. Vyberte **zkontrolovat + vytvořit**, zkontrolujte nasazení a vyberte **vytvořit**.

## <a name="special-case-update-from-10-or-11-to-12"></a>Zvláštní případ: aktualizace z 1,0 nebo 1,1 na 1,2

Počínaje verzí 1,2 služba IoT Edge používá nový název balíčku a obsahuje několik rozdílů v procesu instalace a konfigurace. Pokud máte zařízení IoT Edge se systémem, verze 1,0 nebo 1,1, pomocí těchto pokynů se dozvíte, jak aktualizovat na 1,2.

>[!NOTE]
>V současné době není k dispozici podpora IoT Edge verze 1,2 spuštěná na zařízeních s Windows.

Mezi hlavní rozdíly mezi 1,2 a staršími verzemi patří:

* Název balíčku se změnil z **iotedge** na **aziot-Edge**.
* Balíček **libiothsm-STD** se už nepoužívá. Pokud jste použili standardní balíček, který je součástí verze IoT Edge, pak můžete vaše konfigurace přenést do nové verze. Pokud jste používali jinou implementaci libiothsm-STD, bude nutné překonfigurovat všechny uživatelské certifikáty, jako je certifikát identity zařízení, certifikační autorita zařízení a sada Trust.
* V rámci verze 1,2 byla představena nová služba identity **aziot-identity-Service** . Tato služba zpracovává zřizování a správu identit pro IoT Edge a další součásti zařízení, které potřebují komunikovat s IoT Hub, jako je [aktualizace zařízení pro IoT Hub](../iot-hub-device-update/understand-device-update.md).
* Výchozí konfigurační soubor má nový název a umístění. Ve `/etc/iotedge/config.yaml` výchozím nastavení se teď ve výchozím nastavení očekávají informace o konfiguraci zařízení `/etc/aziot/config.toml` . `iotedge config import`Příkaz lze použít k migraci informací o konfiguraci ze starého umístění a syntaxe do nového.
  * Příkaz import nemůže detekovat ani upravovat pravidla přístupu k čipu TPM (Trusted Platform Module) zařízení. Pokud vaše zařízení používá ověření identity pomocí čipu TPM, je nutné ručně aktualizovat soubor/etc/udev/rules.d/tpmaccess.Rules, aby poskytoval přístup ke službě aziottpm. Další informace najdete v tématu [poskytnutí přístupu k čipu TPM IoT Edge](how-to-auto-provision-simulated-device-linux.md?view=iotedge-2020-11&preserve-view=true#give-iot-edge-access-to-the-tpm).
* Rozhraní API úlohy ve verzi 1,2 ukládá šifrované tajné klíče v novém formátu. Pokud upgradujete ze starší verze na verzi 1,2, bude importován existující hlavní šifrovací klíč. Rozhraní API úlohy může číst tajné kódy uložené v předchozím formátu pomocí importovaného šifrovacího klíče. Rozhraní API pro úlohy ale nemůže zapisovat šifrované tajné klíče ve starém formátu. Jakmile je tajný kód znovu zašifrovaný modulem, uloží se do nového formátu. Tajné klíče šifrované ve verzi 1,2 jsou pro stejný modul ve verzi 1,1 nepřečtené. Pokud zachováte šifrovaná data do složky nebo svazku připojené k hostiteli, vždy *před* upgradem vytvořte záložní kopii dat, aby se zachovala možnost downgrade v případě potřeby.

Před automatizací všech procesů aktualizace ověřte, že funguje na testovacích počítačích.

Až budete připraveni, aktualizujte IoT Edge na svých zařízeních pomocí následujících kroků:

1. Získat nejnovější konfiguraci úložiště od Microsoftu:

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Malina Pi OS Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Zkopírujte vygenerovaný seznam.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Nainstalujte veřejný klíč Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

4. Aktualizujte apt.

   ```bash
   sudo apt-get update
   ```

5. Odinstalujte předchozí verzi IoT Edge a zanechte své konfigurační soubory na místě.

   ```bash
   sudo apt-get remove iotedge
   ```

6. Nainstalujte nejnovější verzi IoT Edge společně se službou identity IoT.

   ```bash
   sudo apt-get install aziot-edge
   ```

7. Naimportujte starý soubor config. yaml do nového formátu a použijte informace o konfiguraci.

   ```bash
   sudo iotedge config import
   ```

Teď, když je služba IoT Edge běžící na vašich zařízeních aktualizovaná, postupujte podle kroků v tomto článku a [aktualizujte také kontejnery modulu runtime](#update-the-runtime-containers).

## <a name="special-case-update-to-a-release-candidate-version"></a>Zvláštní případ: aktualizace na verzi Release Candidate

Azure IoT Edge pravidelně uvolňuje nové verze služby IoT Edge. Před každou stabilní verzí je k dispozici nejméně jedna verze Release Candidate (RC). Verze RC zahrnují všechny plánované funkce pro tuto verzi, ale stále procházejí testováním a ověřováním. Pokud chcete otestovat novou funkci v brzkém případě, můžete nainstalovat verzi RC a poskytnout zpětnou vazbu prostřednictvím GitHubu.

Verze kandidáta Release mají stejnou konvenci číslování verzí, ale mají **-RC** plus přírůstkové číslo připojené ke konci. Kandidáty na vydání verze si můžete prohlédnout ve stejném seznamu [Azure IoT Edge vydání](https://github.com/Azure/azure-iotedge/releases) jako stabilní verze. Například vyhledejte **1.2.0-RC4**, což je jedno z vydaných verzí kandidátů před **1.2.0**. Můžete si také prohlédnout, že verze RC jsou označeny pomocí popisků **před vydáním** .

Agenti IoT Edge a moduly rozbočovače mají verze RC, které jsou označené stejnou konvencí. Například **MCR.Microsoft.com/azureiotedge-hub:1.2.0-RC4**.

Ve verzi Preview jsou Release Candidate verze nezahrnuté jako nejnovější verze, které jsou určené pro běžné instalační programy. Místo toho je nutné ručně cílit prostředky na verzi RC, kterou chcete testovat. Ve většině případů je instalace nebo aktualizace verze RC stejná jako cílená na jinou konkrétní verzi IoT Edge.

V částech v tomto článku se dozvíte, jak aktualizovat zařízení IoT Edge na určitou verzi modulů pro démona zabezpečení nebo modul runtime.

Pokud instalujete IoT Edge, nemusíte upgradovat existující instalaci, použijte postup v části [instalace offline nebo konkrétní verze](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional).

## <a name="next-steps"></a>Další kroky

Prohlédněte si nejnovější [verze Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Udržujte si přehled o nejnovějších aktualizacích a oznámeních na [blogu Internet věcí](https://azure.microsoft.com/blog/topics/internet-of-things/)
