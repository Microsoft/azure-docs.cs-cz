---
title: Povolení zálohování pro Azure Stack z portálu pro správu | Dokumentace Microsoftu
description: Povolte službu Backup infrastruktury prostřednictvím portálu pro správu služby Azure Stack je možné obnovit, pokud dojde k selhání.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 1585eb460cc5f8ae437ee59a596dc7a854a108e7
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995726"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Povolení zálohování pro Azure Stack z portálu pro správu
Povolte službu Backup infrastruktury prostřednictvím portálu pro správu služby Azure Stack může generovat infrastruktura zálohování. Partner hardwaru tyto zálohy lze použít k obnovení svého prostředí pomocí cloudu obnovení v případě [závažnému selhání](./azure-stack-backup-recover-data.md). Účelem zotavení cloudu je zajistit, že uživatelé a operátoři může přihlásit zpátky na portálu po dokončení obnovení. Uživatelé budou mít svá předplatná obnovit včetně oprávnění k přístupu na základě rolí a rolí, původní plány, nabídky a dříve definované výpočetní prostředky, úložiště, síťové kvóty a tajných kódů služby Key Vault.

Ale služba Backup infrastruktury nemá zálohování virtuálních počítačů IaaS, konfigurace sítě a prostředky úložiště, jako jsou účty úložiště, objekty BLOB, tabulky a tak dále, takže uživatelům přihlášení po dokončení obnovení cloudu se nezobrazí žádné z dříve stávající prostředky. Platforma jako služba (PaaS) prostředkům a datům také nezálohují se službou. 

Pro zálohování a obnovení prostředky IaaS a PaaS odděleně od procesů zálohování infrastruktury zodpovídají správci a uživatelé. Informace o zálohování prostředky IaaS a PaaS najdete v následujících tématech:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Povolit nebo změnit konfiguraci zálohování

1. Otevřít [portál pro správu služby Azure Stack](azure-stack-manage-portals.md).
2. Vyberte **všechny služby**a potom v části **správu** vyberte kategorii **infrastruktura zálohování**. Zvolte **konfigurace** v **infrastruktura zálohování** okno.
3. Zadejte cestu k **umístění úložiště zálohy**. Použijte řetězec konvenci UNC (Universal Naming) pro cestu ke sdílené složce hostované na samostatném zařízení. Řetězec ve formátu UNC Určuje umístění prostředků, jako jsou sdílené soubory nebo zařízení. Pro službu můžete použít IP adresu. K zajištění dostupnosti zálohovaných dat po havárii, zařízení by měla být v samostatném umístění.

    > [!Note]  
    > Pokud vaše prostředí podporuje překlad adres v síti infrastruktura Azure stacku podnikovém prostředí, můžete použít plně kvalifikovaný název domény, nikoli IP adresu.

4. Typ **uživatelské jméno** použití domény a uživatelské jméno s dostatečný přístup pro čtení a zápis souborů. Například, `Contoso\backupshareuser`.
5. Typ **heslo** pro daného uživatele.
6. Zadejte heslo znovu **potvrzení hesla**.
7. **Četnost v hodinách** Určuje, jak často se vytvoří zálohy. Výchozí hodnota je 12. Scheduler podporuje maximálně 12 a minimálně 4. 
8. **Doba uchování ve dnech** Určuje, kolik dní záloh, které jsou uvedeny v externím místě. Výchozí hodnota je 7. Scheduler podporuje maximálně 14 a minimálně 2. Starší než doba uchování zálohy se automaticky odstraní z externího umístění.

    > [!Note]  
    > Pokud chcete archivovat starší než doba uchování zálohy, ujistěte se, že chcete-li zálohovat soubory předtím, než Plánovač odstraní zálohy. Pokud je zkrátit období uchovávání záloh (např. ze 7 dní na 5 dní), Plánovač odstraní všechny zálohy, které jsou starší než novým obdobím uchovávání. Ujistěte se, že máte ok se zálohami získávání odstraněn před aktualizací této hodnoty. 

9. V nastavení šifrování zadejte certifikát v souboru certifikátu .cer. Záložní soubory jsou šifrované pomocí veřejného klíče v certifikátu. Byste měli poskytnout certifikát, který obsahuje pouze část s veřejným klíčem, při konfiguraci nastavení zálohování. Po nastavení tohoto certifikátu poprvé nebo otočit certifikát v budoucnu, zobrazí se pouze kryptografický otisk certifikátu. Nelze stáhnout nebo zobrazení souboru nahraný certifikát. Soubor certifikátu vytvoříte spuštěním následujícího příkazu Powershellu k vytvoření certifikátu podepsaného svým držitelem pomocí veřejných a privátních klíčů a vyexportujte certifikát s pouze část s veřejným klíčem.

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

    > [!Note]  
    > **1901 a vyšší**: Azure Stack přijímá certifikát pro šifrování zálohovaných dat infrastruktury. Ujistěte se, že se má certifikát uložit s veřejným i privátním klíčem v zabezpečeném umístění. Z bezpečnostních důvodů nedoporučujeme používání certifikátu s veřejné a privátní klíče ke konfiguraci nastavení zálohování. Další informace o tom, jak spravovat životní cyklus tohoto certifikátu naleznete v tématu [osvědčené postupy služby Backup infrastruktury](azure-stack-backup-best-practices.md).

10. Vyberte **OK** uložte nastavení zálohování kontroleru.

![Azure Stack – nastavení kontroleru zálohování](media/azure-stack-backup/backup-controller-settings-certificate.png)

## <a name="start-backup"></a>Spustit zálohování
Pokud chcete spustit zálohování, klikněte na **zálohovat nyní** spustit zálohu na vyžádání. Zálohu na vyžádání nezmění čas příští plánované zálohování. Po dokončení úlohy můžete potvrdit nastavení v **Essentials**:

![Azure Stack – zálohování na vyžádání](media/azure-stack-backup/scheduled-backup.png)

Můžete taky spustit rutinu prostředí PowerShell **Start AzsBackup** ve vašem počítači pro správu služby Azure Stack. Další informace najdete v tématu [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Povolí nebo zakáže automatické zálohování
Zálohy jsou automaticky naplánované povolit zálohování. Další čas plánu zálohování můžete zkontrolovat **Essentials**. 

![Azure Stack – zálohování na vyžádání](media/azure-stack-backup/on-demand-backup.png)

Pokud je nutné zakázat budoucí naplánovaných záloh, klikněte na **zakázat automatické zálohování**. Zakázat automatické zálohy budete mít nakonfigurované nastavení zálohování a zachová plán zálohování. Tato akce jednoduše dává pokyn, aby vynechat budoucí zálohy. 

![Azure Stack – zakázat naplánované zálohy](media/azure-stack-backup/disable-auto-backup.png)

Potvrďte, že budoucí naplánované zálohy byla zakázána v **Essentials**:

![Azure Stack – potvrďte zálohy jsou zakázané.](media/azure-stack-backup/confirm-disable.png)

Klikněte na **povolit automatické zálohování** informovat plánovač spustit budoucích zálohování v naplánovaném čase. 

![Azure Stack – povolení naplánované zálohy](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Pokud jste nakonfigurovali zálohování infrastruktury před aktualizací na 1807, automatické zálohování se deaktivuje. Tímto způsobem zálohy tím, že Azure Stack nejsou v konfliktu s tím, že úlohu externí modul pro plánování zálohování. Když zakážete všechny externí Plánovač, klikněte na **povolit automatické zálohování**.

## <a name="update-backup-settings"></a>Aktualizovat nastavení zálohování
Od verze 1901 podpora pro šifrovací klíč je zastaralý. Pokud konfigurujete zálohování v 1901 poprvé, musíte použít certifikát. Azure Stack podporuje šifrovacího klíče jenom v případě, že je před aktualizací na 1901 nakonfigurovaný klíč. Zpětná kompatibilita režimu bude pokračovat pro tři verze. Potom se už nepodporuje šifrovací klíče. 

### <a name="default-mode"></a>Výchozí režim
V nastavení šifrování Pokud konfigurujete zálohování infrastruktury poprvé po instalaci nebo aktualizaci 1901, musíte nakonfigurovat zálohování s certifikátem. Pomocí šifrovacího klíče se už nepodporuje. 

Pokud chcete aktualizovat certifikát použitý k šifrování zálohovaných dat, můžete nahrát nový. CER soubor s část s veřejným klíčem a kliknutím na tlačítko OK uložte nastavení. 

Nové zálohování začít používat veřejný klíč v nový certifikát. Neexistuje žádný vliv na všechny stávající zálohy vytvořené pomocí předchozího certifikátu. Ujistěte se, že chcete zachovat starší certifikát kolem v zabezpečeném umístění, v případě, že ho budete potřebovat pro obnovení cloudu.

![Azure Stack – kryptografický otisk certifikátu zobrazení](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Zpětně režim kompatibility
Pokud jste před aktualizací na 1901 nakonfigurovali zálohování, nastavení se přenesou beze změny v chování. V takovém případě šifrovací klíč je podporováno pro zpětnou kompatibilitu. Máte možnost aktualizovat šifrovací klíč nebo přepnutí pro použití certifikátu. Budete mít tři verze, a pokračovat v aktualizaci šifrovací klíč. Pomocí této doby přechodu k certifikátu. 

![Azure Stack – pomocí šifrovacího klíče v režimu zpětné kompatibility](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> Aktualizace z šifrovací klíč k certifikátu je jednosměrná operace. Po provedení této změny, nebudete moci přepnout zpět na šifrovací klíč. Všechny existující zálohy zůstanou šifrovaná pomocí předchozí šifrovacího klíče. 

![Azure Stack – použití šifrovací certifikát v režimu zpětné kompatibility](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>Další postup

Zjistěte, jak spustit zálohování. Zobrazit [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md)

Zjistěte, jak ověřit, jestli vaše záloha spustila. Zobrazit [potvrdit zálohování bylo dokončeno v portálu pro správu](azure-stack-backup-back-up-azure-stack.md)