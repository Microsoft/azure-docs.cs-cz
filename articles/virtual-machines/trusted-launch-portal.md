---
title: 'Verze Preview: nasazení důvěryhodného spouštěcího virtuálního počítače'
description: Nasaďte virtuální počítač, který používá důvěryhodné spuštění.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: dec9c7581bbcf55196b04e0a76e9e61f81a27244
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582064"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Nasazení virtuálního počítače s povoleným důvěryhodným spouštěním (Preview)

[Důvěryhodné spuštění](trusted-launch.md) představuje způsob, jak zlepšit zabezpečení virtuálních počítačů [generace 2](generation-2.md) . Důvěryhodné spuštění chrání před pokročilými a trvalými způsoby útoku kombinováním technologií infrastruktury, jako je vTPM a zabezpečené spouštění.

> [!IMPORTANT]
> Důvěryhodné spuštění je aktuálně ve verzi Public Preview.
> 
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
>
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Nasazení pomocí portálu

Vytvořte virtuální počítač s povoleným důvěryhodným spuštěním.

1. Přihlaste se k webu Azure [Portal](https://aka.ms/TL_preview).
1. Vyhledejte **Virtual Machines**.
1. V části **služby** vyberte **virtuální počítače**.
1. Na stránce **virtuální počítače** vyberte **Přidat** a potom vyberte **virtuální počítač**.
1. V části **Project Details (podrobnosti projektu**) Zkontrolujte, že je vybrané správné předplatné.
1. V části **Skupina prostředků** vyberte **vytvořit novou** a zadejte název skupiny prostředků nebo vyberte existující skupinu prostředků z rozevíracího seznamu.
1. V části **Podrobnosti instance** zadejte název pro název virtuálního počítače a vyberte oblast, která podporuje [důvěryhodné spuštění](trusted-launch.md#public-preview-limitations).
1. V části **Obrázek** vyberte [obrázek, který podporuje důvěryhodné spuštění](trusted-launch.md#public-preview-limitations). Může se zobrazit jenom verze 1. generace obrázku, která je v pořádku, přejděte k dalšímu kroku.
1. Přepněte na kartu **Upřesnit** tím, že ji vyberete v horní části stránky.
1. Přejděte dolů k části **generování virtuálního počítače** a pak vyberte **Obecné 2**.
1. Pořád na kartě **Upřesnit** se posuňte dolů na **důvěryhodné spuštění** a pak zaškrtněte políčko pro **důvěryhodné spuštění** . Tím se zobrazí dvě další možnosti – zabezpečené spouštění a vTPM. Vyberte vhodné možnosti pro vaše nasazení.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Snímek obrazovky znázorňující možnosti pro důvěryhodné spuštění":::

1. Vraťte se na kartu **základy** v části **Image** a ujistěte se, že se zobrazí následující zpráva: **Tento obrázek podporuje důvěryhodné spuštění verze Preview. Nakonfigurujte na kartě Upřesnit**. Nyní by se měla vybrat obrázek 2. generace.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Snímek obrazovky s potvrzením, že se jedná o Gen2 obrázek, který podporuje důvěryhodné spuštění":::

1.  Vyberte velikost virtuálního počítače, která podporuje důvěryhodné spuštění. Podívejte se na seznam [podporovaných velikostí](trusted-launch.md#public-preview-limitations).
1.  Vyplňte informace o **účtu správce** a pak nastavte **pravidla portů pro příchozí spojení**.
1.  V dolní části stránky vyberte **zkontrolovat + vytvořit** .
1.  Na stránce **vytvořit virtuální počítač** můžete zobrazit podrobnosti o virtuálním počítači, který se chystáte nasadit. Až budete připraveni, vyberte **Vytvořit**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Sceenshot stránky ověření, kde jsou uvedeny možnosti důvěryhodných spuštění.":::


Nasazení virtuálního počítače bude několik minut trvat. 

## <a name="deploy-using-a-template"></a>Nasazení pomocí šablony

Pro nasazení důvěryhodných spouštěcích virtuálních počítačů můžete použít šablonu pro rychlý Start:

**Linux**:    
[![Nasadit do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Nasadit do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Zobrazit a aktualizovat

Konfiguraci důvěryhodných spuštění pro existující virtuální počítač můžete zobrazit návštěvou stránky s **přehledem** pro virtuální počítač na portálu.

Pokud chcete změnit konfiguraci důvěryhodného spuštění, v levé nabídce vyberte v části **Nastavení** možnost **Konfigurace** . Zabezpečené spouštění a vTPM můžete povolit nebo zakázat v části **důvěryhodných spuštění** . Po dokončení vyberte **Uložit** v horní části stránky. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Snímek obrazovky s postupem, jak změnit konfiguraci důvěryhodného spuštění.":::

Pokud je virtuální počítač spuštěný, zobrazí se zpráva, že se virtuální počítač restartuje, aby se používala upravená konfigurace důvěryhodného spuštění. Vyberte **Ano** a počkejte, než se virtuální počítač restartuje, aby se změny projevily.


## <a name="verify-secure-boot-and-vtpm"></a>Ověření zabezpečeného spouštění a vTPM

Můžete ověřit, jestli jsou na virtuálním počítači povolené zabezpečené spouštění a vTPM.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: Ověřte, zda je spuštěno zabezpečené spouštění.

Připojte se k virtuálnímu počítači přes SSH a pak spusťte následující příkaz: 

```bash
mokutil --sb-state
```

Pokud je povolené zabezpečené spouštění, příkaz vrátí:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: ověření, zda je povoleno vTPM

Připojte se k virtuálnímu počítači přes SSH. Ověřte, jestli je k dispozici zařízení tpm0: 

```bash
ls /dev/tpm0
```

Pokud je povolený vTPM, příkaz vrátí:

```output
/dev/tpm0
```

Pokud je vTPM zakázaný, příkaz se vrátí:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: Ověřte, zda je spuštěno zabezpečené spouštění.

Připojte se k virtuálnímu počítači pomocí vzdálené plochy a potom spusťte `msinfo32.exe` .

V pravém podokně ověřte, zda je **zapnutý** stav zabezpečeného spuštění.

## <a name="enable-the-azure-security-center-experience"></a>Povolit prostředí Azure Security Center

Pokud chcete povolit, aby Azure Security Center zobrazovaly informace o vašich důvěryhodných spouštěcích virtuálních počítačích, musíte povolit několik zásad. Nejjednodušší způsob, jak tyto zásady povolit, je nasadit tuto [šablonu správce prostředků](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) do svého předplatného. 

Kliknutím na tlačítko dole nasadíte zásady do svého předplatného:

[![Nasadit do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

Šablona musí být nasazena pouze jednou pro každé předplatné. Automaticky se nainstaluje `GuestAttestation` a `AzureSecurity` rozšíření na všechny podporované virtuální počítače. Pokud se zobrazí chyby, zkuste šablonu znovu nasadit.

Pokud chcete získat doporučení pro vTPM a zabezpečené spouštění pro důvěryhodné spouštěcí virtuální počítače, přečtěte si téma [Přidání vlastní iniciativy do předplatného](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription).
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Podepisování věcí pro zabezpečené spouštění systému Linux

V některých případech může být nutné se přihlásit k zabezpečenému spouštění UEFI.  Například budete potřebovat [postup, jak se přihlašovat k zabezpečenému spouštění](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) pro Ubuntu. V těchto případech je potřeba zadat pro svůj virtuální počítač klíče pro zápis MOK Utility. K tomu je potřeba použít pro přístup k nástroji MOK službu sériového portu Azure.

1. Povolte službu Azure Serial Console pro Linux. Další informace najdete v tématu [sériová Konzola pro Linux](/troubleshoot/azure/virtual-machines/serial-console-linux).
1. Přihlaste se k [Azure Portal](https://portal.azure.com).
1. Vyhledejte **virtuální počítače** a ze seznamu vyberte svůj virtuální počítač.
1. V nabídce vlevo v části **Podpora a řešení potíží** vyberte **sériová konzola**. Stránka se otevře napravo s použitím konzoly sériového portu.
1. Přihlaste se k virtuálnímu počítači pomocí sériové konzole Azure. Jako **přihlašovací údaje** zadejte uživatelské jméno, které jste použili při vytváření virtuálního počítače. Například *azureuser*. Po zobrazení výzvy zadejte heslo přidružené k uživatelskému jménu.
1. Po přihlášení použijte `mokutil` k importu souboru veřejného klíče `.der` .

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Restartujte počítač ze služby Azure Serial Console zadáním příkazu `sudo reboot` . Začne se 10 sekundový odpočítávání.
1. Stisknutím klávesy šipka nahoru nebo dolů přerušte odpočítávání a počkejte v režimu konzoly rozhraní UEFI. Pokud nedojde k přerušení časovače, proces spouštění pokračuje a všechny změny MOK budou ztraceny.
1. V nabídce nástroje MOK vyberte příslušnou akci.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Snímek obrazovky znázorňující dostupné možnosti v nabídce Správa MOK v konzole sériového portu.":::


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o virtuálních počítačích pro [důvěryhodné spuštění](trusted-launch.md) a [generaci 2](generation-2.md) .