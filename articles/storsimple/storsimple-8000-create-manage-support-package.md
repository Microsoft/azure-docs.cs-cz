---
title: Vytvoření balíčku podpory řady StorSimple 8000
description: Naučte se vytvářet, dešifrovat a upravovat balíček pro podporu pro zařízení řady StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4a847b273472ecc9d2aaa3993ec9d88aa46f2e7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017164"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Vytvoření a Správa balíčku pro podporu pro řady StorSimple 8000

## <a name="overview"></a>Přehled

Balíček podpory StorSimple je snadno použitelný mechanismus, který shromažďuje všechny relevantní protokoly, které vám pomůžou podpora Microsoftu s řešením potíží s problémy s StorSimple zařízeními. Shromážděné protokoly jsou zašifrované a komprimované.

Tento kurz obsahuje podrobné pokyny k vytvoření a správě balíčku pro podporu pro zařízení řady StorSimple 8000. Pokud pracujete s virtuálním polem StorSimple, přečtěte si, jak [vygenerovat balíček protokolu](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Vytvoření balíčku pro podporu

V některých případech budete muset ručně vytvořit balíček pro podporu prostřednictvím Windows PowerShell pro StorSimple. Například:

* Pokud potřebujete před sdílením pomocí podpora Microsoftu odebrat citlivé informace ze svých souborů protokolu.
* Pokud máte potíže při nahrávání balíčku z důvodu problémů s připojením.

Ručně vytvořený balíček podpory můžete sdílet s podpora Microsoftu přes e-mail. Chcete-li vytvořit balíček pro podporu v nástroji Windows PowerShell pro StorSimple, proveďte následující kroky.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Postup vytvoření balíčku pro podporu v nástroji Windows PowerShell pro StorSimple

1. Pokud chcete spustit relaci Windows PowerShellu jako správce na vzdáleném počítači, který se používá pro připojení k zařízení StorSimple, zadejte následující příkaz:
   
    `Start PowerShell`
2. V relaci Windows PowerShellu se připojte ke konzole SSAdmin vašeho zařízení:
   
   1. Na příkazovém řádku zadejte:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. V dialogovém okně, které se otevře, zadejte heslo správce zařízení. Výchozí heslo je _Heslo1_.
     
      ![Dialogové okno přihlašovací údaje prostředí PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Vyberte **OK**.
   4. Na příkazovém řádku zadejte:
     
      `Enter-PSSession $MS`
3. V relaci, která se otevře, zadejte příslušný příkaz.
   
   * Pro sdílené síťové složky, které jsou chráněny heslem, zadejte:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Zobrazí se výzva k zadání hesla a šifrovacího hesla (protože balíček pro podporu je zašifrovaný). Balíček pro podporu se pak vytvoří ve výchozí složce (název zařízení se připojil s aktuálním datem a časem).
   * U sdílených složek, které nejsou chráněny heslem, nepotřebujete `-Credential` parametr. Zadejte následující:
     
       `Export-HcsSupportPackage`
     
       Balíček pro podporu se vytvoří pro oba řadiče ve výchozí složce. Balíček je zašifrovaný komprimovaný soubor, který je možné odeslat podpora Microsoftu k řešení potíží. Další informace najdete v tématu [kontakt podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Parametry rutiny Export-HcsSupportPackage

Pomocí rutiny Export-HcsSupportPackage můžete použít následující parametry.

| Parametr | Požadováno/volitelné | Popis |
| --- | --- | --- |
| `-Path` |Povinné |Slouží k zadání umístění sdílené síťové složky, ve které je balíček podpory umístěný. |
| `-EncryptionPassphrase` |Vyžadováno |Použijte k poskytnutí přístupového hesla, který vám může pomoci s šifrováním balíčku pro podporu. |
| `-Credential` |Volitelné |Slouží k zadání přihlašovacích údajů pro přístup ke sdílené síťové složce. |
| `-Force` |Volitelné |Použijte k přeskočení potvrzovacího kroku šifrovacího hesla. |
| `-PackageTag` |Volitelné |Použijte k určení adresáře v *cestě* , ve které je balíček pro podporu umístěný. Výchozí hodnota je [název zařízení]-[aktuální datum a čas: RRRR-MM-DD-HH-MM-SS]. |
| `-Scope` |Volitelné |Určete jako **cluster** (výchozí) pro vytvoření balíčku pro podporu pro oba řadiče. Pokud chcete vytvořit balíček jenom pro aktuální kontroler, zadejte **kontroler**. |

## <a name="edit-a-support-package"></a>Úprava balíčku pro podporu

Po vygenerování balíčku pro podporu může být nutné upravit balíček pro odebrání citlivých informací. To může zahrnovat názvy svazků, IP adresy zařízení a názvy záloh ze souborů protokolu.

> [!IMPORTANT]
> Balíček podpory, který byl vygenerován prostřednictvím Windows PowerShell pro StorSimple, lze upravit pouze. Balíček vytvořený v Azure Portal nelze upravovat pomocí služby StorSimple Správce zařízení.

Chcete-li upravit balíček podpory před tím, než ho nahrajete na podpora Microsoftu lokalitu, nejprve dešifrujte balíček podpory, upravte soubory a pak ho znovu zašifrujte. Proveďte následující kroky.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Postup úpravy balíčku pro podporu v nástroji Windows PowerShell pro StorSimple

1. Vygenerujte balíček pro podporu, jak je popsáno výše, v tématu [Vytvoření balíčku pro podporu v nástroji Windows PowerShell pro StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Stáhněte si skript](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) místně na svém klientovi.
3. Importujte modul prostředí Windows PowerShell. Zadejte cestu k místní složce, do které jste stáhli skript. Pokud chcete modul naimportovat, zadejte:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Všechny soubory jsou komprimované a šifrované soubory *. AES* . Chcete-li dekomprimovat a dešifrovat soubory, zadejte:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Všimněte si, že aktuální přípony souborů se teď zobrazují pro všechny soubory.
   
    ![Upravit balíček pro podporu](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Až budete vyzváni k zadání šifrovacího hesla, zadejte heslo, které jste použili při vytváření balíčku podpory.
   
    ```powershell
    cmdlet Open-HcsSupportPackage at command pipeline position 1

    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
6. Přejděte do složky, která obsahuje soubory protokolu. Vzhledem k tomu, že soubory protokolu jsou nyní dekomprimovány a dešifrovány, budou mít původní přípony souborů. Úpravou těchto souborů odeberte informace specifické pro zákazníka, jako jsou názvy svazků a IP adresy zařízení, a soubory uložte.
7. Zavřete soubory pro jejich komprimaci pomocí nástroje gzip a zašifrujte je pomocí AES-256. Jedná se o rychlost a zabezpečení při přenosu balíčku pro podporu přes síť. Chcete-li zkomprimovat a zašifrovat soubory, zadejte následující:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Upravit balíček podpory 2](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Po zobrazení výzvy zadejte šifrovací heslo pro upravený balíček pro podporu.
   
    ```powershell
    cmdlet Close-HcsSupportPackage at command pipeline position 1
    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
9. Zapište si nové přístupové heslo, abyste ho mohli při vyžádání sdílet s podpora Microsoftu.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Příklad: úprava souborů v balíčku pro podporu na sdílené složce chráněné heslem

Následující příklad ukazuje, jak dešifrovat, upravit a znovu zašifrovat balíček pro podporu.

```powershell
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
```

## <a name="next-steps"></a>Další kroky

* Informace o [informacích shromažďovaných v balíčku pro podporu](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Naučte se [používat balíčky podpory a protokoly zařízení k řešení potíží s nasazením zařízení](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Naučte se [používat službu StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

