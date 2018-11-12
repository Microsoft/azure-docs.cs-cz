---
title: Vytvořit balíček pro podporu StorSimple 8000 series | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit, dešifrování a upravit balíček pro podporu pro vaše zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: dfc2d8d763a1eb64a37af73e03992f2d948a6856
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254362"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Vytvoření a Správa balíčku pro podporu pro řadu StorSimple 8000

## <a name="overview"></a>Přehled

Balíček pro podporu StorSimple se snadným ovládáním mechanismus, který shromažďuje všechny relevantní protokoly pro účely pomoci Microsoft Support s řešit jakékoli problémy zařízení StorSimple. Shromážděné protokoly jsou zašifrované a komprimované.

Tento kurz obsahuje podrobné pokyny k vytváření a správě balíček pro podporu pro vaše zařízení řady StorSimple 8000. Pokud pracujete se službou StorSimple Virtual Array, přejděte na [vygenerujte balíček pro protokol](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Vytvořit balíček pro podporu

V některých případech budete muset ručně vytvořit balíček pro podporu prostředí Windows PowerShell pro StorSimple. Příklad:

* Pokud chcete odebrání citlivých informací ze souborů protokolu se před sdílením s Microsoft Support.
* Pokud máte potíže nahrává se balíček kvůli problémům s připojením.

Váš balíček pro podporu ručně generované můžete sdílet s Microsoft Support prostřednictvím e-mailu. Provedení následujících kroků vytvořte balíček pro podporu v prostředí Windows PowerShell pro StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Chcete-li vytvořit balíček pro podporu v prostředí Windows PowerShell pro StorSimple

1. Spusťte relaci prostředí Windows PowerShell jako správce na vzdáleném počítači, který se používá pro připojení k zařízení StorSimple, zadejte následující příkaz:
   
    `Start PowerShell`
2. V relaci Windows Powershellu připojení ke konzole SSAdmin vašeho zařízení:
   
   1. Na příkazovém řádku zadejte:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. V dialogovém okně, které se otevře zadejte heslo správce zařízení. Výchozí heslo je _Heslo1_.
     
      ![Dialogové okno přihlašovacích údajů prostředí PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Vyberte **OK**.
   4. Na příkazovém řádku zadejte:
     
      `Enter-PSSession $MS`
3. V této relaci, která se otevře zadejte příslušný příkaz.
   
   * Pro sdílené síťové složky, které jsou chráněné heslem zadejte:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       (Protože je zašifrovaný balíček pro podporu) budete vyzváni k zadání hesla a šifrovací heslo. Balíček pro podporu se pak vytvoří ve výchozí složce (název zařízení s aktuálním datem a časem).
   * Pro sdílené složky, které se nechrání pomocí hesla, není nutné `-Credential` parametru. Zadejte následující:
     
       `Export-HcsSupportPackage`
     
       Pro oba kontrolery ve výchozí složce se vytvoří balíček pro podporu. Balíček je zašifrované, komprimované soubor, který je možné odeslat společnosti Microsoft Support pro řešení potíží. Další informace najdete v tématu [kontaktujte podporu Microsoftu](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Parametry rutiny Export-HcsSupportPackage rutiny

Pomocí rutiny Export-HcsSupportPackage rutiny můžete použít následující parametry.

| Parametr | Požadované a volitelné | Popis |
| --- | --- | --- |
| `-Path` |Požaduje se |Můžete zadat umístění síťové sdílené složce, ve kterém je umístí balíček pro podporu. |
| `-EncryptionPassphrase` |Požaduje se |Pomocí této možnosti zadejte heslo pro pomůže zašifrovat balíček pro podporu. |
| `-Credential` |Nepovinné |Slouží k poskytování přístupové přihlašovací údaje pro sdílené síťové složce. |
| `-Force` |Nepovinné |Můžete přeskočit krok potvrzení hesla šifrování. |
| `-PackageTag` |Nepovinné |Použijte k určení adresáře v rámci *cesta* v balíčku pro podporu nachází. Výchozí hodnota je [název]-[aktuální datum a time:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Nepovinné |Zadejte jako **clusteru** (výchozí), chcete-li vytvořit balíček pro podporu pro oba kontrolery. Pokud chcete vytvořit balíček pouze pro aktuální kontroler, zadejte **řadič**. |

## <a name="edit-a-support-package"></a>Upravit balíček pro podporu

Po vygenerování balíčku pro podporu, možná budete muset upravit balíček odebrání citlivých informací. To může zahrnovat názvy svazků, IP adresy zařízení a zálohy názvy v souborech protokolů.

> [!IMPORTANT]
> Lze upravit pouze balíček pro podporu, který byl vygenerován pomocí prostředí Windows PowerShell pro StorSimple. Balíček vytvořili na webu Azure Portal pomocí služby Správce zařízení StorSimple se nedá upravit.

Upravit balíček pro podporu před nahráním na webu Microsoft Support, nejprve dešifrovat balíček pro podporu, upravovat soubory a pak znovu zašifrovat. Proveďte následující kroky.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Chcete-li upravit balíček pro podporu v prostředí Windows PowerShell pro StorSimple

1. Vygenerujte balíček pro podporu jak je popsáno dříve v [vytvořit balíček pro podporu v prostředí Windows PowerShell pro StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Stáhněte si skript](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) místně na klientovi.
3. Naimportujte modul Windows Powershellu. Zadejte cestu k místní složce, ve které jste si stáhli skriptu. Chcete-li importovat modul, zadejte:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Všechny soubory jsou *.aes* soubory, které jsou komprimovaná a šifrovaná. Pro dekompresi a dešifrovat soubory, zadejte:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Všimněte si, že skutečný soubor rozšíření se teď zobrazují pro všechny soubory.
   
    ![Upravit balíček pro podporu.](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Po zobrazení výzvy pro šifrovací heslo, zadejte heslo, které jste použili při vytváření balíčku pro podporu.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Přejděte do složky, která obsahuje soubory protokolu. Protože soubory protokolu jsou teď dekomprimovat a dešifrovat, ty budou mít původní přípon souborů. Tyto soubory odebrat jakékoli informace specifické pro zákazníka, jako jsou názvy svazků a IP adresy zařízení upravovat a ukládat soubory.
7. Zavřete soubory, které chcete komprimovat pomocí gzip a šifrování pomocí AES-256. Jde o rychlost a zabezpečení v balíčku pro podporu přenos přes síť. Chcete-li komprese a šifrování souborů, zadejte následující:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Upravit balíček pro podporu.](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Po zobrazení výzvy zadejte šifrovací heslo pro upravené podpůrný balíček.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Zapište si nové heslo, tak, aby ji můžete sdílet s Microsoft Support na požádání.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Příklad: Úpravy souborů v balíčku pro podporu ve sdílené složce chráněné heslem

Následující příklad ukazuje, jak k dešifrování, upravit a znovu zašifrovat balíček pro podporu.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Další postup

* Další informace o [informacemi shromážděnými balíček pro podporu.](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Zjistěte, jak [použít balíčky podpory a protokoly zařízení k řešení potíží s nasazením zařízení](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Zjistěte, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

