---
title: Vytvoření balíčku podpory řady StorSimple 8000
description: Přečtěte si, jak vytvořit, dešifrovat a upravit balíček podpory pro zařízení řady StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 9ca033f6f786c0142261dafa31b93b71a8b3336a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277075"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Vytvoření a správa balíčku podpory pro řadu StorSimple 8000

## <a name="overview"></a>Přehled

Balíček podpory StorSimple je snadno použitelný mechanismus, který shromažďuje všechny příslušné protokoly, které pomáhají podpoře společnosti Microsoft při řešení problémů se zařízením StorSimple. Shromážděné protokoly jsou šifrovány a komprimovány.

Tento kurz obsahuje podrobné pokyny k vytvoření a správě balíčku podpory pro zařízení řady StorSimple 8000. Pokud pracujete s virtuálním polem StorSimple, přejděte ke [generování balíčku protokolu](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Vytvoření balíčku podpory

V některých případech budete muset ručně vytvořit balíček podpory prostřednictvím prostředí Windows PowerShell pro StorSimple. Například:

* Pokud potřebujete odebrat citlivé informace ze souborů protokolu před sdílením s podporou společnosti Microsoft.
* Pokud máte potíže s nahráváním balíčku kvůli problémům s připojením.

Ručně generovaný balíček podpory můžete sdílet s podporou Microsoftu přes e-mail. Pomocí následujících kroků vytvořte balíček podpory v prostředí Windows PowerShell pro StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Vytvoření balíčku podpory v prostředí Windows PowerShell pro StorSimple

1. Chcete-li spustit relaci prostředí Windows PowerShell jako správce ve vzdáleném počítači, který se používá k připojení k zařízení StorSimple, zadejte následující příkaz:
   
    `Start PowerShell`
2. V relaci prostředí Windows PowerShell se připojte ke konzole SSAdmin vašeho zařízení:
   
   1. Na příkazovém řádku zadejte:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. V zobrazeném dialogovém okně zadejte heslo správce zařízení. Výchozí heslo je _Password1_.
     
      ![Dialogové okno pověření prostředí PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Vyberte **OK**.
   4. Na příkazovém řádku zadejte:
     
      `Enter-PSSession $MS`
3. V relaci, která se otevře, zadejte příslušný příkaz.
   
   * U síťových sdílených složek, které jsou chráněny heslem, zadejte:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Budete vyzváni k zadání hesla a šifrovacího přístupového hesla (protože balíček podpory je šifrovaný). Balíček podpory je pak vytvořen ve výchozí složce (Název zařízení připojený k aktuálnímu datu a času).
   * Pro sdílené složky, které nejsou chráněny `-Credential` heslem, nepotřebujete parametr. Zadejte následující:
     
       `Export-HcsSupportPackage`
     
       Balíček podpory je vytvořen pro oba řadiče ve výchozí složce. Balíček je šifrovaný komprimovaný soubor, který lze odeslat do podpory společnosti Microsoft pro řešení potíží. Další informace naleznete v [tématu Contact Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Parametry rutiny Rutina Export-HcsSupportPackage

Následující parametry můžete použít s rutinou Export-HcsSupportPackage.

| Parametr | Požadováno/volitelné | Popis |
| --- | --- | --- |
| `-Path` |Požaduje se |Slouží k zadání umístění sdílené síťové složky, do které je umístěn balíček podpory. |
| `-EncryptionPassphrase` |Požaduje se |Slouží k zadání přístupové fráze, která vám pomůže zašifrovat balíček podpory. |
| `-Credential` |Nepovinné |Slouží k zadání přístupových pověření pro sdílenou síťovou složku. |
| `-Force` |Nepovinné |Slouží k přeskočení kroku potvrzení hesla. |
| `-PackageTag` |Nepovinné |Slouží k určení adresáře v části *Cesta,* ve kterém je umístěn balíček podpory. Výchozí hodnota je [název zařízení]-[aktuální datum a čas:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Nepovinné |Zadejte jako **cluster** (výchozí) pro vytvoření balíčku podpory pro oba řadiče. Pokud chcete vytvořit balíček pouze pro aktuální řadič, zadejte **Řadič**. |

## <a name="edit-a-support-package"></a>Úprava balíčku podpory

Po vygenerování balíčku podpory může být nutné upravit balíček k odebrání citlivých informací. To může zahrnovat názvy svazků, IP adresy zařízení a názvy záloh ze souborů protokolu.

> [!IMPORTANT]
> Můžete upravit pouze balíček podpory, který byl vygenerován prostřednictvím prostředí Windows PowerShell pro StorSimple. Balíček vytvořený na webu Azure Portal nelze upravit pomocí služby StorSimple Device Manager.

Chcete-li upravit balíček podpory před jeho nahráním na web podpory společnosti Microsoft, nejprve balíček podpory dešifrujte, upravte soubory a znovu jej zašifrujte. Proveďte následující kroky.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Úprava balíčku podpory v prostředí Windows PowerShell pro StorSimple

1. Vygenerujte balíček podpory, jak je popsáno výše, v [chcete-li vytvořit balíček podpory v prostředí Windows PowerShell pro StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Stáhněte si skript](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) místně na vašem klientovi.
3. Importujte modul Prostředí Windows PowerShell. Zadejte cestu k místní složce, do které jste skript stáhli. Chcete-li modul importovat, zadejte:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Všechny soubory jsou *.aes* soubory, které jsou komprimované a šifrované. Chcete-li dekomprimovat a dešifrovat soubory, zadejte:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Všimněte si, že skutečné přípony souborů jsou nyní zobrazeny pro všechny soubory.
   
    ![Upravit balíček podpory](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Až se zobrazí výzva k zadání šifrovacího hesla, zadejte přístupové heslo, které jste použili při vytvoření balíčku podpory.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Přejděte do složky, která obsahuje soubory protokolu. Vzhledem k tomu, že soubory protokolu jsou nyní dekomprimovány a dešifrovány, budou mít původní přípony souborů. Upravte tyto soubory tak, aby byly všechny informace specifické pro zákazníka, například názvy svazků a IP adresy zařízení, a uložte soubory.
7. Zavřete soubory, abyste je komprimovali pomocí gzip a zašifrovali je pomocí AES-256. To to je pro rychlost a zabezpečení při přenosu balíčku podpory přes síť. Chcete-li soubory komprimovat a zašifrovat, zadejte následující:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Upravit balíček podpory](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Po zobrazení výzvy zadejte šifrovací přístupové heslo pro upravený balíček podpory.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Zapište si nové přístupové heslo, abyste je mohli na požádání sdílet s podporou společnosti Microsoft.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Příklad: Úpravy souborů v balíčku podpory ve sdílené složce chráněné heslem

Následující příklad ukazuje, jak dešifrovat, upravit a znovu zašifrovat balíček podpory.

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

## <a name="next-steps"></a>Další kroky

* Informace o [informacích shromážděných v balíčku podpory](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Přečtěte si, jak [pomocí balíčků podpory a protokolů zařízení řešit potíže s nasazením zařízení](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Přečtěte si, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

