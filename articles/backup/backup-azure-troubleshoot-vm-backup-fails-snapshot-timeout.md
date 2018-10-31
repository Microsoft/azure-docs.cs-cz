---
title: 'Řešení potíží se selháním Azure Backup: hostovaného agenta stav nedostupný'
description: Příznaky, příčiny a řešení Azure Backup chyby týkající se agenta, rozšíření a disky.
services: backup
author: genlin
manager: cshepard
keywords: Služba Azure backup; Agent virtuálního počítače; Připojení k síti;
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 5c37e2e3cabb81ed123146f283c7d568cc58816d
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242618"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Řešení potíží se selháním Azure Backup: problémy s agentů nebo rozšíření

Tento článek popisuje postup řešení potíží, které vám může pomoct vyřešit Azure Backup chyby týkající se komunikace s agentem virtuálního počítače a rozšíření.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>Agent virtuálního počítače moci komunikovat se službou Azure Backup

Chybová zpráva: "Agent virtuálního počítače moci komunikovat se službou Azure Backup"<br>
Kód chyby: "UserErrorGuestAgentStatusUnavailable"

Po registraci a plánování virtuálních počítačů za službu Backup zahájí zálohování úlohy tím, že komunikuje s agentem virtuálního počítače k vytvoření snímku bodu v čase. Snímek některý z následujících podmínek může zabránit se aktivuje. Když se aktivuje snímku, zálohování se nemusí podařit. Proveďte následující kroky odstraňování potíží v uvedeném pořadí a opakujte operaci:

**1. příčina: [virtuální počítač nemá přístup k Internetu](#the-vm-has-no-internet-access)**  
**2. příčina: [agent je nainstalovaný ve virtuálním počítači, ale jeho reagovat (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**3. příčina: [agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**4. příčina: [stavu snímku nelze načíst ani nemůže být pořídí snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**5 příčina: [rozšíření zálohování se nezdaří pro aktualizaci nebo načtení](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Operace vytvoření snímku selhala, protože virtuální počítač není připojený k síti

Chybová zpráva: "Snímku operace se nezdařila z důvodu žádné připojení k síti na virtuálním počítači"<br>
Kód chyby: "ExtensionSnapshotFailedNoNetwork"

Po registraci a naplánovat virtuálního počítače pro služby Azure Backup zahájí zálohování úlohy tím, že komunikuje s virtuálního počítače rozšíření zálohování k vytvoření snímku bodu v čase. Snímek některý z následujících podmínek může zabránit se aktivuje. Pokud se snímek se aktivuje, může dojít k selhání zálohování. Proveďte následující kroky odstraňování potíží v uvedeném pořadí a opakujte operaci:    
**1. příčina: [virtuální počítač nemá přístup k Internetu](#the-vm-has-no-internet-access)**  
**2. příčina: [stavu snímku nelze načíst ani nemůže být pořídí snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**3. příčina: [rozšíření zálohování se nezdaří pro aktualizaci nebo načtení](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>Operace rozšíření vmsnapshot selhala

Chybová zpráva: "operace rozšíření VMSnapshot selhala"<br>
Kód chyby: "ExtentionOperationFailed"

Po registraci a naplánovat virtuálního počítače pro služby Azure Backup zahájí zálohování úlohy tím, že komunikuje s virtuálního počítače rozšíření zálohování k vytvoření snímku bodu v čase. Snímek některý z následujících podmínek může zabránit se aktivuje. Pokud se snímek se aktivuje, může dojít k selhání zálohování. Proveďte následující kroky odstraňování potíží v uvedeném pořadí a opakujte operaci:  
**1. příčina: [stavu snímku nelze načíst ani nemůže být pořídí snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. příčina: [rozšíření zálohování se nezdaří pro aktualizaci nebo načtení](#the-backup-extension-fails-to-update-or-load)**  
**3. příčina: [agent je nainstalovaný ve virtuálním počítači, ale jeho reagovat (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**4. příčina: [agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>Zálohování selhalo, protože agent virtuálního počítače nereaguje

Chybová zpráva: "Nejde komunikovat s agentem virtuálního počítače kvůli stavu snímku" <br>
Kód chyby: "GuestAgentSnapshotTaskStatusError"

Po registraci a naplánovat virtuálního počítače pro služby Azure Backup zahájí zálohování úlohy tím, že komunikuje s virtuálního počítače rozšíření zálohování k vytvoření snímku bodu v čase. Snímek některý z následujících podmínek může zabránit se aktivuje. Pokud se snímek se aktivuje, může dojít k selhání zálohování. Proveďte následující kroky odstraňování potíží v uvedeném pořadí a opakujte operaci:  
**1. příčina: [agent je nainstalovaný ve virtuálním počítači, ale jeho reagovat (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. příčina: [agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. příčina: [virtuální počítač nemá přístup k Internetu](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>Zálohování selhalo s interní chybou

Chybová zpráva: "Zálohování selhalo s interní chybou – Zkuste prosím tuto operaci za několik minut." <br>
Kód chyby: "BackUpOperationFailed" / "BackUpOperationFailedV2"

Po registraci a naplánovat virtuálního počítače pro služby Azure Backup zahájí zálohování úlohy tím, že komunikuje s virtuálního počítače rozšíření zálohování k vytvoření snímku bodu v čase. Snímek některý z následujících podmínek může zabránit se aktivuje. Pokud se snímek se aktivuje, může dojít k selhání zálohování. Proveďte následující kroky odstraňování potíží v uvedeném pořadí a opakujte operaci:  
**1. příčina: [virtuální počítač nemá přístup k Internetu](#the-vm-has-no-internet-access)**  
**2. příčina: [nereaguje agent nainstalovaný ve virtuálním počítači, ale (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**3. příčina: [agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**4. příčina: [stavu snímku nelze načíst ani nemůže být pořídí snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**5 příčina: [rozšíření zálohování se nezdaří pro aktualizaci nebo načtení](#the-backup-extension-fails-to-update-or-load)**  
**Příčina 6: [služba zálohování nemá oprávnění odstranit staré body obnovení z důvodu zámku skupiny prostředků](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="the-vm-has-no-internet-access"></a>Virtuální počítač nemá přístup k Internetu
Za požadavek na nasazení virtuální počítač nemá přístup k Internetu. Nebo může mít omezení, které brání přístupu infrastrukturou Azure.

Správné fungování rozšíření Backup vyžaduje připojení k veřejným IP adresám Azure. Rozšíření odesílá příkazy do služby Azure storage koncový bod (adresy URL HTTPs) ke správě snímky virtuálního počítače. Pokud přípona nemá přístup k veřejným Internetem, zálohování nakonec dojde k chybě.

Je možné nasadit proxy server pro směrování provozu virtuálního počítače.
##### <a name="create-a-path-for-https-traffic"></a>Vytvoření cesty pro komunikaci přes protokol HTTPs

1. Pokud máte síťových omezení na místě (například skupinu zabezpečení sítě), nasadíte server proxy HTTPs ke směrování provozu.
2. Pokud chcete povolit přístup k Internetu prostřednictvím serveru proxy protokolu HTTPs, přidáte pravidla skupiny zabezpečení sítě, pokud nemáte.

Informace o nastavení serveru proxy protokolu HTTPs pro zálohy virtuálních počítačů najdete v tématu [Příprava prostředí pro zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

Zálohované virtuální počítač nebo proxy server, přes který se směruje provoz vyžaduje přístup k Azure, veřejné IP adresy

####  <a name="solution"></a>Řešení
Chcete-li vyřešit tento problém, zkuste použijte jeden z následujících metod:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Povolit přístup k úložišti Azure, která odpovídá na oblast

Můžete použít [značky služeb](../virtual-network/security-overview.md#service-tags) umožňující připojení k úložišti určité oblasti. Ujistěte se, že pravidlo, které umožňuje přístup k účtu úložiště má vyšší prioritu než pravidla, která zablokuje přístup internet. 

![Skupina zabezpečení sítě se značkami úložiště pro oblast](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Podívejte se na informace o tom postup krok za krokem konfigurace značky služeb, [toto video](https://youtu.be/1EjLQtbKm1M).

> [!WARNING]
> Značky služeb úložiště jsou ve verzi preview. Jsou k dispozici pouze v konkrétní oblasti. Seznam oblastí naleznete v tématu [značky pro úložiště služeb](../virtual-network/security-overview.md#service-tags).

Pokud používáte Azure Managed Disks, může být nutné počáteční další portu (port 8443) na bránu firewall.

Kromě toho pokud podsíť nemá trasy pro odchozí přenosy z Internetu, musíte přidat koncový bod služby se značka služby "Microsoft.Storage" pro vaši podsíť. 

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent je nainstalovaný na virtuálním počítači, ale přestane reagovat (pro virtuální počítače s Windows)

#### <a name="solution"></a>Řešení
Agent virtuálního počítače může být poškozený nebo služba zastavila. Opětovná instalace agenta virtuálního počítače pomáhá získat nejnovější verzi. Pomáhá také restartovat komunikaci se službou.

1. Určení, zda se službou Windows agenta hosta virtuálního počítače službami (services.msc). Pokuste se restartovat službu agenta hosta Windows a zahajte zálohování.    
2. Pokud službu agenta hosta Windows nejsou viditelná ve službách, v Ovládacích panelech, přejděte na **programy a funkce** k určení, zda je nainstalována služba Windows agenta hosta.
4. Pokud se zobrazí v agentovi hosta Windows **programy a funkce**, odinstalujte agenta hosta Windows.
5. Stáhněte a nainstalujte [nejnovější verzi MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Musíte mít oprávnění správce k dokončení instalace.
6. Ověřte, že služby agenta hosta Windows služby.
7. Spusťte zálohu na vyžádání: 
    * Na portálu vyberte **zálohovat nyní**.

Kromě toho ověřte, že [je nainstalované rozhraní Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ve virtuálním počítači. Rozhraní .NET 4.5 je vyžadován pro agenta virtuálního počítače ke komunikaci se službou.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)

#### <a name="solution"></a>Řešení
Většina souvisejících s agenty nebo souvisejících s rozšířením selhání pro virtuální počítače s Linuxem jsou způsobeny problémy, které ovlivňují zastaralé agenta virtuálního počítače. Chcete-li tento problém vyřešit, postupujte podle následujících obecných pokynů:

1. Postupujte podle pokynů pro [aktualizace agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Jsme *důrazně doporučujeme* , že aktualizujete agenta pouze prostřednictvím distribuce úložiště. Nedoporučujeme stažením kódu agenta přímo z Githubu a aktualizacích. Pokud nejnovější verzi agenta pro vaši distribuci není k dispozici, požádejte distribuce podporu pokyny o tom, jak ji nainstalovat. Vyhledat nejnovější agent, přejděte [Windows Azure Linux agent](https://github.com/Azure/WALinuxAgent/releases) stránky v úložišti GitHub.

2. Ujistěte se, že Azure agent běží na virtuálním počítači spuštěním následujícího příkazu: `ps -e`

 Pokud proces není spuštěn, můžete ji restartujte pomocí následujících příkazů:

 * Pro Ubuntu: `service walinuxagent start`
 * Pro jiné distribuce: `service waagent start`

3. [Konfigurace automatického restartování agenta](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Spusťte nové zálohování testu. Pokud chyba přetrvává, shromážděte následující protokoly z virtuálního počítače:

   * /var/lib/waagent/*.XML
   * /var/log/waagent.log
   * / var/protokolu/azure / *

Pokud pro waagent vyžadujeme podrobné protokolování, postupujte podle těchto kroků:

1. V souboru /etc/waagent.conf vyhledejte následující řádek: **zapnout podrobné protokolování (y | n)**
2. Změnit **Logs.Verbose** hodnotu *n* k *y*.
3. Uložte změny a potom restartujte waagent provedením kroků popsaných dříve v této části.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nelze načíst stav snímku, nebo nelze pořídí snímek
Zálohování virtuálních počítačů se spoléhá na vydání příkazu snímku do podkladového účtu úložiště. Zálohování může selhat, protože nemá přístup k účtu úložiště, nebo zpoždění spuštění úlohy snímku.

#### <a name="solution"></a>Řešení
Následující podmínky mohou způsobit selhání úlohy snímku:

| Příčina | Řešení |
| --- | --- |
| Stav virtuálního počítače je uvedena nesprávně, protože virtuální počítač je vypnutý v protokolu RDP (Remote Desktop). | Pokud vypnete virtuální počítač v protokolu RDP, podívejte se na portál k určení, zda je stav virtuálního počítače správný. Pokud není správný, vypněte virtuální počítač na portálu pomocí **vypnutí** možnost na řídicím panelu virtuálních počítačů. |
| Virtuální počítač nelze získat adresu hostitele nebo prostředků infrastruktury ze serveru DHCP. | DHCP musí být povolené uvnitř hosta pro zálohování virtuálních počítačů IaaS pro práci. Pokud virtuální počítač nemůže získat adresu hostitele nebo prostředků infrastruktury z odpovědi DHCP 245, se nedá stáhnout nebo ji spustit žádná rozšíření. Pokud potřebujete statickou privátní IP adresu, můžete ji nakonfigurujte prostřednictvím platformy. Možnosti DHCP ve virtuálním počítači musí být povolené vlevo. Další informace najdete v tématu [nastavit statickou privátní IP interní](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>Rozšíření zálohování se nezdaří pro aktualizaci nebo načtení
Pokud rozšíření nelze načíst, zálohování se nezdaří, protože nelze pořídí snímek.

#### <a name="solution"></a>Řešení

Odinstalujte rozšíření přinutit rozšíření VMSnapshot znovu načíst. Další pokus o zálohování znovu načte rozšíření.

Chcete-li odinstalovat rozšíření:

1. V [webu Azure portal](https://portal.azure.com/), přejděte k virtuálnímu počítači, na kterém dochází k selhání zálohování.
2. Vyberte **nastavení**.
3. Vyberte **Extensions** (Rozšíření).
4. Vyberte **rozšíření Vmsnapshot**.
5. Vyberte **odinstalovat**.

Pro virtuální počítač s Linuxem, pokud rozšíření VMSnapshot není uveden na webu Azure Portal [aktualizace agenta Azure Linux](../virtual-machines/linux/update-agent.md), a pak spusťte zálohování. 

Dokončení tohoto postupu způsobí, že rozšíření znovu při dalším zálohování.

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>Služba Backup nemá oprávnění k odstranění staré body obnovení z důvodu zámku skupiny prostředků
Tento problém je specifický pro spravované virtuální počítače, ve kterých se uživatel uzamkne skupinu prostředků. Služba backup v tomto případě nelze odstranit starší body obnovení. Protože je limit 18 bodů obnovení, začínají selhávat nových záloh.

#### <a name="solution"></a>Řešení

Chcete-li problém vyřešit, odeberte zámek ze skupiny prostředků a proveďte následující kroky, chcete-li odebrat kolekci bodů obnovení: 
 
1. Odeberte zámek proti ve skupině prostředků, ve kterém se nachází virtuální počítač. 
2. Instalace ARMClient pomocí Chocolatey: <br>
   https://github.com/projectkudu/ARMClient
3. Přihlaste se k ARMClient: <br>
    `.\armclient.exe login`
4. Získáte kolekci bodů obnovení, odpovídající virtuální počítač: <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Příklad: `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. Odstraňte kolekci bodů obnovení: <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. Další naplánované zálohování automaticky vytvoří kolekci bodů obnovení a nové body obnovení.

Až to bude hotové, můžete znovu do zpět zámek skupina prostředků virtuálního počítače. 
