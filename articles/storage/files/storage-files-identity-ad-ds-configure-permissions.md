---
title: Určení toho, co může uživatel provádět na úrovni souboru – sdílené složky Azure
description: Naučte se konfigurovat oprávnění Windows ACL pro místní služba AD DS ověřování pro sdílené složky Azure. Umožní vám využít podrobnější řízení přístupu.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 02b8d72ab88f9eca2e1fac4858c14826dae57dbe
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629168"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Třetí část: Konfigurace oprávnění adresářů a souborů přes SMB 

Než začnete s tímto článkem, ujistěte se, že jste dokončili předchozí článek, [přiřaďte identitě oprávnění na úrovni sdílené složky](storage-files-identity-ad-ds-assign-permissions.md) a ujistěte se, že jsou nastavená vaše oprávnění na úrovni sdílené složky.

Po přiřazení oprávnění na úrovni sdílení k Azure RBAC je nutné nakonfigurovat správné seznamy ACL pro Windows na úrovni kořenového adresáře, adresáře nebo souboru, aby bylo možné využít přesnější řízení přístupu. Oprávnění na úrovni sdílené složky Azure RBAC si můžete představit jako gatekeeper na vysoké úrovni, který určuje, jestli uživatel může ke sdílené složce přistupovat. I když seznamy ACL systému Windows fungují na podrobnější úrovni, aby bylo možné určit, které operace může uživatel provádět na úrovni adresáře nebo souboru. Oprávnění na úrovni sdílené složky a souboru nebo adresáře se vynutily, když se uživatel pokusí o přístup k souboru nebo adresáři, takže pokud mezi nimi existuje rozdíl, použije se jenom ta, která je jenom nejpřísnější. Například pokud má uživatel přístup pro čtení a zápis na úrovni souboru, ale přečte se jenom na úrovni sdílení, pak ho můžou číst jenom. Totéž by platilo, pokud bylo obrácené a uživatel měl přístup pro čtení a zápis na úrovni sdílené složky, ale jenom číst na úrovni souboru, ale může ho jenom číst.

## <a name="azure-rbac-permissions"></a>Oprávnění Azure RBAC

Následující tabulka obsahuje oprávnění Azure RBAC související s touto konfigurací:


| Předdefinovaná role  | Oprávnění NTFS  | Výsledný přístup  |
|---------|---------|---------|
|Čtenář sdílené složky SMB dat souboru úložiště | Úplné řízení, úprava, čtení, zápis, provedení | Read & execute  |
|     |   Čtení |     Čtení  |
|Přispěvatel sdílené složky SMB dat souboru úložiště  |  Full control    |  Upravit, číst, zapsat, spustit |
|     |  Modify         |  Modify    |
|     |  Read & execute |  Read & execute |
|     |  Čtení           |  Čtení    |
|     |  Zápis          |  Zápis   |
|Přispěvatel sdílené složky SMB dat souboru úložiště s vyššími oprávněními | Full control  |  Upravit, číst, zapsat, upravit, spustit |
|     |  Modify          |  Modify |
|     |  Read & execute  |  Read & execute |
|     |  Čtení            |  Čtení   |
|     |  Zápis           |  Zápis  |



## <a name="supported-permissions"></a>Podporovaná oprávnění

Soubory Azure podporují úplnou sadu základních a rozšířených seznamů ACL pro Windows. Můžete zobrazit a nakonfigurovat seznamy řízení přístupu (ACL) systému Windows u adresářů a souborů ve sdílené složce Azure připojením sdílené složky a následným použitím Průzkumníka souborů Windows, spuštěním příkazu Windows [Icacls](/windows-server/administration/windows-commands/icacls) nebo příkazu [set-ACL](/powershell/module/microsoft.powershell.security/set-acl) . 

Pokud chcete nakonfigurovat seznamy řízení přístupu (ACL) pomocí uživatelských oprávnění, musíte sdílenou složku připojit pomocí klíče účtu úložiště na VIRTUÁLNÍm počítači připojeném k doméně. Podle pokynů v následující části připojte sdílenou složku Azure z příkazového řádku a nakonfigurujte seznamy řízení přístupu (ACL) systému Windows.

Do kořenového adresáře sdílené složky jsou zahrnutá tato oprávnění:

- BUILTIN\Administrators: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (v/v) (GR, GE)
- Uživatelé NT Authority\authenticated Users: (OI) – CI (M)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (F)
- CREATOR OWNER: (OI) (CI) (V/V) (F)

|Uživatelé|Definice|
|---|---|
|BUILTIN\Administrators|Všichni uživatelé, kteří jsou správci domény Prem služba AD DSho prostředí.
|BUILTIN\Users|Integrovaná skupina zabezpečení ve službě AD. Zahrnuje uživatele NT Authority\authenticated Users ve výchozím nastavení. Pro tradiční souborový server můžete nakonfigurovat definici členství na server. Pro soubory Azure není k dispozici hostitelský server, takže BUILTIN\Users zahrnuje stejnou skupinu uživatelů jako uživatelé NT Authority\authenticated Users.|
|NT AUTHORITY\SYSTEM|Účet služby pro operační systém souborového serveru. Takový účet služby se v kontextu souborů Azure nepoužívá. Je zahrnutý do kořenového adresáře, aby byl konzistentní s prostředím Windows Files Server pro hybridní scénáře.|
|Uživatelé NT Authority\authenticated Users|Všichni uživatelé ve službě AD, kteří můžou získat platný token protokolu Kerberos.|
|VLASTNÍK AUTORA|Každý objekt má buď adresář, nebo soubor vlastník tohoto objektu. Pokud jsou pro daný objekt k tomuto objektu přiřazeny seznamy řízení přístupu (CREATOR OWNER), má uživatel, který je vlastníkem tohoto objektu, oprávnění k objektu definovanému seznamem ACL.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Připojení sdílení souborů z příkazového řádku

`net use`K připojení sdílené složky Azure použijte příkaz Windows. Nezapomeňte nahradit zástupné hodnoty v následujícím příkladu vlastními hodnotami. Další informace o připojení sdílených složek najdete v tématu [použití sdílené složky Azure v systému Windows](storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Pokud dochází k potížím s připojením k souborům Azure, přečtěte si [Nástroj pro řešení potíží, který jsme publikovali pro chyby připojení k souborům Azure ve Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Poskytujeme také [pokyny](./storage-files-faq.md#on-premises-access) pro řešení scénářů při zablokování portu 445. 

## <a name="configure-windows-acls"></a>Konfigurace seznamů řízení přístupu systému Windows

Jakmile je sdílená složka připojená k klíči účtu úložiště, musíte nakonfigurovat seznamy řízení přístupu (ACL) systému Windows (označované také jako oprávnění systému souborů NTFS). Seznamy ACL pro Windows můžete nakonfigurovat buď pomocí Průzkumníka souborů Windows, nebo Icacls.

Pokud máte adresáře nebo soubory na místních souborových serverech s Windows DACL nakonfigurovanými na služba AD DS identity, můžete je zkopírovat do souborů Azure, které uchovávají seznamy ACL pomocí tradičních nástrojů pro kopírování souborů, jako je Robocopy nebo [Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases). Pokud jsou vaše adresáře a soubory vrstveny ve službě soubory Azure prostřednictvím Azure File Sync, seznamy ACL se přenesou a ukládají v jejich nativním formátu.

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Konfigurace seznamů řízení přístupu (ACL) Windows pomocí Průzkumníka souborů Windows

Pomocí Průzkumníka souborů Windows udělte úplným oprávněním všem adresářům a souborům ve sdílené složce, včetně kořenového adresáře.

1. Otevřete Průzkumníka souborů Windows, klikněte pravým tlačítkem na soubor nebo adresář a vyberte **vlastnosti**.
1. Vyberte kartu **zabezpečení** .
1. Vyberte **Upravit...** ke změně oprávnění.
1. Můžete změnit oprávnění stávajících uživatelů nebo vybrat **Přidat...** a udělit jim oprávnění novým uživatelům.
1. V okně příkazového řádku pro přidání nových uživatelů zadejte cílové uživatelské jméno, kterému chcete udělit oprávnění, do pole **Zadejte názvy objektů k výběru** a vyberte možnost **kontrolovat názvy** a vyhledejte úplný název UPN cílového uživatele.
1.    Vyberte **OK**.
1.    Na kartě **zabezpečení** vyberte všechna oprávnění, která chcete novému uživateli udělit.
1.    Vyberte **Apply** (Použít).

### <a name="configure-windows-acls-with-icacls"></a>Konfigurace seznamů ACL pro Windows pomocí icacls

K udělení úplných oprávnění všem adresářům a souborům ve sdílené složce, včetně kořenového adresáře, použijte následující příkaz Windows. Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Další informace o tom, jak používat icacls k nastavení seznamů řízení přístupu (ACL) systému Windows a různých typů podporovaných oprávnění, najdete v tématu [Reference k příkazovému řádku pro icacls](/windows-server/administration/windows-commands/icacls).

## <a name="next-steps"></a>Další kroky

Teď, když je funkce povolená a nakonfigurovaná, přejděte k dalšímu článku, kde připojíte sdílenou složku Azure k virtuálnímu počítači připojenému k doméně.

[4. část: připojení sdílené složky z virtuálního počítače připojeného k doméně](storage-files-identity-ad-ds-mount-file-share.md)