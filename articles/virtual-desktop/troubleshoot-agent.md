---
title: Řešení potíží s agentem virtuálních počítačů s Windows – Azure
description: Jak vyřešit běžné problémy s agentem a připojením.
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 2f321413a275676d0abb1a075ba958885ffcd821
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505021"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>Řešení běžných problémů s agentem virtuálních počítačů s Windows

Agent virtuálních počítačů s Windows může způsobovat problémy s připojením kvůli několika faktorům:
   - Ve zprostředkovateli došlo k chybě, která způsobuje, že agent zastaví službu.
   - Problémy s aktualizacemi.
   - Problémy s instalací během instalace agenta, které narušují připojení k hostiteli relace.

Tento článek vás provede řešeními těchto běžných scénářů a postupem, jak řešit problémy s připojením.

>[!NOTE]
>Pro řešení potíží souvisejících s připojením relace a agentem virtuálního počítače s Windows doporučujeme zkontrolovat protokoly událostí v **Prohlížeč událostí**  >  **aplikace Windows logs**  >  . Vyhledejte události, které mají jeden z následujících zdrojů k identifikaci vašeho problému:
>
>- WVD-Agent
>- WVD-agent – aktualizační program
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>Chyba: zavaděč RDAgentBootLoader a/nebo program Agent pro vzdálenou plochu přestal běžet.

Pokud vidíte některý z následujících problémů, znamená to, že zaváděcí program pro spouštění, který načte agenta, nemohl nainstalovat agenta správně a služba agenta neběží:
- **RDAgentBootLoader** je buď zastavená, nebo nespuštěná.
- Pro **zavaděč agenta vzdálené plochy** neexistuje žádný stav.

Chcete-li tento problém vyřešit, spusťte zaváděcí program RDAgent boot:

1. V okně služby klikněte pravým tlačítkem na **zavaděč nástroje Remote Desktop agent**.
2. Vyberte **Spustit**. Pokud je tato možnost pro vás šedá, nemáte oprávnění správce a budete je muset využít ke spuštění služby.
3. Počkejte 10 sekund a pak klikněte pravým tlačítkem na **zavaděč nástroje Remote Desktop agent**.
4. Vyberte **aktualizovat**.
5. Pokud se služba po spuštění a aktualizaci zastaví, může dojít k selhání registrace. Další informace najdete v tématu [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token).

## <a name="error-invalid_registration_token"></a>Chyba: INVALID_REGISTRATION_TOKEN

Přejít na **Prohlížeč událostí**  >  **Windows logs**  >  **aplikace**. Pokud se zobrazí událost s ID 3277, která říká **INVALID_REGISTRATION_TOKEN** v popisu, registrační token, který jste nerozpoznali jako platný.

Pokud chcete tento problém vyřešit, vytvořte platný registrační token:

1. Pokud chcete vytvořit nový registrační token, postupujte podle kroků v části [Vytvoření nového registračního klíče pro virtuální počítač](#step-3-generate-a-new-registration-key-for-the-vm) .
2. Otevřete Editor registru. 
3. Přejít na **HKEY_LOCAL_MACHINE**  >  **software**  >  **Microsoft**  >  **RDInfraAgent**.
4. Vyberte možnost **registrováno**. 
5. V poli **data hodnoty:** vstup zadejte **0** a vyberte **OK**. 
6. Vyberte **RegistrationToken**. 
7. V poli **data hodnoty:** vstup vložte registrační token z kroku 1. 

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s registrem 0](media/isregistered-token.png)

8. Otevřete příkazový řádek jako správce.
9. Zadejte **net stop RDAgentBootLoader**. 
10. Zadejte **příkaz net start RDAgentBootLoader**. 
11. Otevřete Editor registru.
12. Přejít na **HKEY_LOCAL_MACHINE**  >  **software**  >  **Microsoft**  >  **RDInfraAgent**.
13. Ověřte, **že** je v poli je nastavená hodnotu 1 a ve sloupci data pro **RegistrationToken** není nic. 

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s registrem 1](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>Chyba: Agent se nemůže připojit ke službě Broker pomocí INVALID_FORM

Přejít na **Prohlížeč událostí**  >  **Windows logs**  >  **aplikace**. Pokud se zobrazí událost s ID 3277, která uvádí "INVALID_FORM" v popisu, došlo k chybě při komunikaci mezi agentem a zprostředkovatelem. Agent se nemůže připojit ke zprostředkovateli nebo získat konkrétní adresu URL z důvodu konkrétního nastavení brány firewall nebo DNS.

Pokud chcete tento problém vyřešit, ověřte, že máte přístup k BrokerURI a BrokerURIGlobal:
1. Otevřete Editor registru. 
2. Přejít na **HKEY_LOCAL_MACHINE**  >  **software**  >  **Microsoft**  >  **RDInfraAgent**. 
3. Poznamenejte si hodnoty pro **BrokerURI** a **BrokerURIGlobal**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s identifikátorem URI zprostředkovatele a globálním identifikátorem URI zprostředkovatele](media/broker-uri.png)

 
4. Otevřete prohlížeč a přejdete na *\<BrokerURI\> rozhraní API/stav*. 
   - Ujistěte se, že používáte hodnotu z kroku 3 v **BrokerURI**. V tomto příkladu by to bylo <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> .
5. V prohlížeči otevřete jinou kartu a pak přejít na *\<BrokerURIGlobal\> rozhraní API/stav*. 
   - Ujistěte se, že jste v odkazu **BrokerURIGlobal** použili hodnotu z kroku 3. V tomto příkladu by to bylo <https://rdbroker.wvd.microsoft.com/api/health> .
6. Pokud síť neblokuje připojení zprostředkovatele, obě stránky se úspěšně načtou a zobrazí se zpráva s oznámením, že **zprostředkovatel VP je v pořádku** , jak je znázorněno na následujících snímcích obrazovky. 

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s úspěšně načteným přístupem k identifikátoru URI zprostředkovatele](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky úspěšně načtený globální přístup k identifikátoru URI zprostředkovatele](media/broker-global.png)
 

7. Pokud síť blokuje připojení zprostředkovatele, stránky se nenačte, jak je znázorněno na následujícím snímku obrazovky. 

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky neúspěšného načteného zprostředkovatele přístupu](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky neúspěšného načteného globálního přístupu zprostředkovatele](media/unsuccessful-broker-global.png)

8. Pokud síť blokuje tyto adresy URL, budete muset odblokovat požadované adresy URL. Další informace najdete v tématu [požadovaný seznam adres URL](safe-url-list.md).
9. Pokud se tím problém nevyřeší, ujistěte se, že nemáte žádné zásady skupiny s šiframi, které blokují agenta pro připojení k zprostředkovateli. Virtuální počítač s Windows používá stejné šifry TLS 1,2 jako [přední dveře Azure](../frontdoor/front-door-faq.yml#what-are-the-current-cipher-suites-supported-by-azure-front-door-). Další informace najdete v tématu [zabezpečení připojení](network-connectivity.md#connection-security).

## <a name="error-3703"></a>Chyba: 3703

Přejít na **Prohlížeč událostí**  >  **Windows logs**  >  **aplikace**. Pokud se zobrazí událost s ID 3703, která uvádí, že adresa Brána VP URL: není dostupná v popisu, Agent nemůže kontaktovat adresy URL brány. Chcete-li se úspěšně připojit k hostiteli relace a povolíte síťovému přenosu do těchto koncových bodů omezení, je nutné odblokovat adresy URL ze [seznamu požadované adresy URL](safe-url-list.md). Zajistěte také, aby nastavení brány firewall nebo proxy neblokovala tyto adresy URL. Odblokování těchto adres URL je nutné pro použití virtuálního klienta Windows.

Pokud chcete tento problém vyřešit, ověřte, že nastavení brány firewall nebo DNS neblokují tyto adresy URL:
1. [K ochraně nasazení virtuálních ploch Windows použijte Azure firewall](../firewall/protect-windows-virtual-desktop.md)..
2. Nakonfigurujte [nastavení DNS Azure firewall](../firewall/dns-settings.md).

## <a name="error-3019"></a>Chyba: 3019

Přejít na **Prohlížeč událostí**  >  **Windows logs**  >  **aplikace**. Pokud se zobrazí událost s ID 3019, znamená to, že agent se nemůže připojit k adresám URL přenosu webového soketu. Chcete-li se úspěšně připojit k hostiteli relace a dovolit v síťovém provozu obejít Tato omezení, musíte odblokovat adresy URL uvedené v [seznamu požadované adresy URL](safe-url-list.md). Pracujte s týmem sítě Azure a ujistěte se, že nastavení brány firewall, proxy serveru a DNS neblokují tyto adresy URL. V protokolech trasování sítě můžete také zjistit, kde je služba Windows Virtual Desktop blokovaná. Pokud otevřete žádost o podporu tohoto konkrétního problému, nezapomeňte připojit protokoly trasování sítě k žádosti.

## <a name="error-installationhealthcheckfailedexception"></a>Chyba: InstallationHealthCheckFailedException

Přejít na **Prohlížeč událostí**  >  **Windows logs**  >  **aplikace**. Pokud se zobrazí událost s ID 3277, která v popisu říká "InstallationHealthCheckFailedException", znamená to, že naslouchací proces zásobníku nefunguje, protože terminálový server přepnul klíč registru pro naslouchací proces zásobníku.

Řešení tohoto problému:
1. Zkontrolujte, zda [naslouchací proces zásobníku pracuje](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
2. Pokud naslouchací proces zásobníku nefunguje, [ručně odinstalujte a znovu nainstalujte součást zásobníku](#error-vms-are-stuck-in-unavailable-or-upgrading-state).

## <a name="error-endpoint_not_found"></a>Chyba: ENDPOINT_NOT_FOUND

Přejít na **Prohlížeč událostí**  >  **Windows logs**  >  **aplikace**. Pokud se zobrazí událost s ID 3277, která v popisu říká "ENDPOINT_NOT_FOUND", znamená to, že zprostředkovatel nemohl najít koncový bod pro navázání spojení. K tomuto problému s připojením může dojít z některého z následujících důvodů:

- Ve fondu hostitelů nejsou žádné virtuální počítače.
- Virtuální počítače ve fondu hostitelů nejsou aktivní.
- Všechny virtuální počítače ve fondu hostitelů překročily maximální limit relace.
- Žádný z virtuálních počítačů ve fondu hostitelů nemá spuštěnou službu agenta.

Řešení tohoto problému:

1. Ujistěte se, že virtuální počítač je zapnutý a nebyl odebraný z fondu hostitelů.
2. Ujistěte se, že virtuální počítač nepřekročil maximální limit relace.
3. Ujistěte se, že [je služba agenta spuštěná](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running) a že [naslouchací proces zásobníku pracuje](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
4. Ujistěte [se, že se agent může připojit ke službě Broker](#error-agent-cannot-connect-to-broker-with-invalid_form).
5. Ujistěte se, že [váš virtuální počítač má platný registrační token](#error-invalid_registration_token).
6. Ujistěte [se, že token registrace virtuálního počítače nevypršel](faq.md#how-often-should-i-turn-my-vms-on-to-prevent-registration-issues). 

## <a name="error-installmsiexception"></a>Chyba: InstallMsiException

Přejít na **Prohlížeč událostí**  >  **Windows logs**  >  **aplikace**. Pokud se zobrazí událost s ID 3277, která říká **InstallMsiException** v popisu, instalační program je již spuštěn pro jinou aplikaci, když se pokoušíte nainstalovat agenta, nebo zásada blokuje spuštění programu msiexec.exe.

Chcete-li tento problém vyřešit, zakažte následující zásady:
   - Vypnout Instalační služba systému Windows  
      - Cesta ke kategorii: Konfigurace počítače \ šablony \ Windows \ instalační program
   
>[!NOTE]
>Nejedná se o úplný seznam zásad, jenom o těch, které v tuto chvíli znáte.

Postup zakázání zásady:
1. Otevřete příkazový řádek jako správce.
2. Zadejte a spusťte **sadu RSoP. msc**.
3. V okně **Výsledná sada zásad** , které se zobrazí, přejdete do cesty kategorie.
4. Vyberte zásadu.
5. Vyberte **Zakázáno**.
6. Vyberte **Použít**.   

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky Instalační služba systému Windows zásady v výsledné sadě zásad](media/gpo-policy.png)

## <a name="error-win32exception"></a>Chyba: Win32Exception

Přejít na **Prohlížeč událostí**  >  **Windows logs**  >  **aplikace**. Pokud se zobrazí událost s ID 3277, která říká **InstallMsiException** v popisu, zásada brání spuštění cmd.exe. Blokování tohoto programu vám znemožní spustit okno konzoly. to je to, co je potřeba použít k restartování služby při každém aktualizaci agenta.

Chcete-li tento problém vyřešit, zakažte následující zásady:
   - Zabránění přístupu k příkazovému řádku   
      - Cesta ke kategorii: uživatel pro správu \ uživatelské šablony
    
>[!NOTE]
>Nejedná se o úplný seznam zásad, jenom o těch, které v tuto chvíli znáte.

Postup zakázání zásady:
1. Otevřete příkazový řádek jako správce.
2. Zadejte a spusťte **sadu RSoP. msc**.
3. V okně **Výsledná sada zásad** , které se zobrazí, přejdete do cesty kategorie.
4. Vyberte zásadu.
5. Vyberte **Zakázáno**.
6. Vyberte **Použít**.   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>Chyba: naslouchací proces zásobníku nefunguje na virtuálním počítači s Windows 10 2004.

Na příkazovém řádku spusťte **qwinsta** a poznamenejte si číslo verze, které se zobrazí vedle **protokolu RDP-SxS**. Pokud si nejste jisti, že komponenty **RDP-TCP** a **RDP-SxS** nastanou **naslouchat** , nebo se nezobrazuje vůbec po spuštění **qwinsta**, znamená to, že dojde k potížím se zásobníkem. Aktualizace zásobníku se nainstalují spolu s aktualizacemi agenta a když se tato instalace Awry, nebude naslouchací proces virtuálních klientů Windows fungovat.

Řešení tohoto problému:
1. Otevřete Editor registru.
2. Přejděte na **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**.
3. V části **WinStations** se může zobrazit několik složek pro různé verze zásobníku. Vyberte složku, která odpovídá informacím o verzi, které jste viděli při spuštění **qwinsta** na příkazovém řádku.
4. Vyhledejte **fReverseConnectMode** a ujistěte se, že hodnota dat je **1**. Také se ujistěte, že je **fEnableWinStation** nastavené na hodnotu **1**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s fReverseConnectMode](media/fenable-2.png)

5. Pokud **fReverseConnectMode** není nastavené na **1**, vyberte **fReverseConnectMode** a do pole hodnota zadejte **1** . 
6. Pokud **fEnableWinStation** není nastavené na hodnotu **1**, vyberte **fEnableWinStation** a do pole hodnota zadejte **1** .
7. Restartujte virtuální počítač. 

>[!NOTE]
>Pokud chcete změnit režim **fReverseConnectMode** nebo **FEnableWinStation** pro více virtuálních počítačů najednou, můžete udělat jednu z následujících dvou věcí:
>
>- Exportujte klíč registru z počítače, který už máte funkční, a naimportujte ho do všech ostatních počítačů, které tuto změnu potřebují.
>- Vytvořte objekt zásad skupiny (GPO), který nastaví hodnotu klíče registru pro počítače, které vyžadují změnu.

7. Přejděte na **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **ClusterSettings**.
8. V části **ClusterSettings** vyhledejte **SessionDirectoryListener** a ujistěte se, že hodnota dat je **RDP-SxS..**..
9. Pokud **SessionDirectoryListener** není nastavené na **RDP-SxS...**, budete muset postupovat podle kroků v části [odinstalace agenta a zaváděcího zavaděče](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) , abyste nejdřív odinstalovali agenta, zaváděcí program pro spouštění a komponenty stacku a pak [znovu přeinstalovali agenta a spouštěcí zavaděč](#step-4-reinstall-the-agent-and-boot-loader). Dojde k přeinstalování souběžného zásobníku.

## <a name="error-heartbeat-issue-where-users-keep-getting-disconnected-from-session-hosts"></a>Chyba: potíže s prezenčním signálem, kdy uživatelé udržují odpojení od hostitelů relací

Pokud váš server neumožňuje vyzvednutí prezenčního signálu ze služby Virtual Desktop systému Windows, bude nutné změnit prahovou hodnotu prezenčního signálu. Tím se dočasně sníží riziko problému, ale neopraví se základní problém sítě. Postupujte podle pokynů v této části, pokud se vám na vás vztahují některé z následujících scénářů:

- Zobrazuje se chyba **CheckSessionHostDomainIsReachableAsync**
- Zobrazuje se chyba **ConnectionBrokenMissedHeartbeatThresholdExceeded**
- Zobrazuje se chyba **ConnectionEstablished: UnexpectedNetworkDisconnect**
- Klienti uživatelů udržují odpojení
- Uživatelé budou mít pořád odpojeni od hostitelů relací.

Postup změny prahové hodnoty prezenčního signálu:
1. Otevřete příkazový řádek jako správce.
2. Zadejte příkaz **qwinsta** a spusťte ho.
3. Měly by se zobrazit dvě komponenty zásobníku: **RDP-TCP** a **RDP-SxS**. 
   - V závislosti na verzi operačního systému, který používáte, může být **RDP-SxS** následováno číslem sestavení. Pokud je, nezapomeňte toto číslo vypsat pro pozdější verzi.
4. Otevřete Editor registru.
5. Přejděte na **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**.
6. V části **WinStations** se může zobrazit několik složek pro různé verze zásobníku. Vyberte složku, která odpovídá číslu verze z kroku 3.
7. Kliknutím pravým tlačítkem na Editor registru vytvořte novou hodnotu DWORD registru a pak vyberte **Nová**  >  **Hodnota DWORD (32 bitů)**. Při vytváření hodnoty DWORD zadejte následující hodnoty:
   - HeartbeatInterval: 10000
   - HeartbeatWarnCount: 30 
   - HeartbeatDropCount: 60 
8. Restartujte virtuální počítač.

>[!NOTE]
>Pokud se při změně prahové hodnoty prezenčního signálu problém nevyřeší, může se stát, že budete mít základní problém se sítí, který budete muset kontaktovat od týmu sítě Azure.

## <a name="error-downloadmsiexception"></a>Chyba: DownloadMsiException

Přejít na **Prohlížeč událostí**  >  **Windows logs**  >  **aplikace**. Pokud se zobrazí událost s ID 3277, která říká **DownloadMsiException** v popisu, na disku není dost místa pro RDAgent.

Pokud chcete tento problém vyřešit, udělejte místo na disku:
   - Odstraňují se soubory, které už nejsou v uživatelském prostředí.
   - Zvýšení kapacity úložiště virtuálního počítače

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>Chyba: Agent se nepodařilo aktualizovat pomocí MissingMethodException.

Přejít na **Prohlížeč událostí**  >  **Windows logs**  >  **aplikace**. Pokud se zobrazí událost s ID 3389, která říká "MissingMethodException: metoda nebyla nalezena" v popisu, znamená to, že agent virtuálního počítače Windows se neaktualizoval úspěšně a vrátil se na dřívější verzi. Důvodem může být to, že číslo verze rozhraní .NET Framework, které je aktuálně nainstalované na virtuálních počítačích, je nižší než 4.7.2. Chcete-li tento problém vyřešit, je nutné upgradovat rozhraní .NET na verzi 4.7.2 nebo novější podle pokynů k instalaci v [dokumentaci .NET Framework](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>Chyba: virtuální počítače jsou zablokované v nedostupném stavu nebo stav upgradu

Otevřete okno PowerShellu jako správce a spusťte následující rutinu:

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

Pokud stav uvedený pro hostitele relace nebo hostitelé ve fondu hostitelů vždycky říká "nedostupný" nebo "upgradování", agent nebo zásobník se nenainstaloval úspěšně.

Pokud chcete tento problém vyřešit, přeinstalujte souběžný zásobník:
1. Otevřete příkazový řádek jako správce.
2. Zadejte **net stop RDAgentBootLoader**. 
3. Otevřete **Ovládací panely** programy programy  >    >  **a funkce**.
4. Odinstalujte nejnovější verzi **síťového zásobníku SxS služby Vzdálená plocha** nebo verzi uvedenou v **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations** v části **ReverseConnectListener**.
5. Otevřete okno konzoly jako správce a pokračujte na **Program Files**  >  **Microsoft RDInfra**.
6. Vyberte součást **SxSStack** nebo spuštěním příkazu **msiexec/i SxSStack- <version> . msi** nainstalujte soubor MSI.
8. Restartujte virtuální počítač.
9. Vraťte se do příkazového řádku a spusťte příkaz **qwinsta** .
10. Ověřte, že součást zásobníku nainstalovaná v kroku 6 říká, že je vedle ní **naslouchat** .
   - Pokud ano, zadejte na příkazovém řádku příkaz **net start RDAgentBootLoader** a restartujte virtuální počítač.
   - V takovém případě budete muset [virtuální počítač znovu zaregistrovat a znovu nainstalovat součást agenta](#your-issue-isnt-listed-here-or-wasnt-resolved) .

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>Chyba: připojení se nenašlo: RDAgent nemá aktivní připojení ke zprostředkovateli.

Vaše virtuální počítače můžou být na limitu připojení, takže virtuální počítač nemůže přijmout nová připojení.

Řešení tohoto problému:
   - Snižte maximální limit relace. Tím se zajistí, že prostředky budou rovnoměrně rozloženy mezi hostitele relací a zabrání se vyčerpání prostředků.
   - Zvyšte kapacitu prostředků virtuálních počítačů.

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>Chyba: provoz virtuálního počítače sady pro nebo jiného nepodporovaného operačního systému

Souběžný zásobník je podporovaný jenom SKU Windows Enterprise nebo Windows serveru, což znamená, že operační systémy, jako je třeba virtuální počítač pro, nejsou. Pokud nemáte SKU v podniku nebo na serveru, zásobník se nainstaluje na virtuální počítač, ale neaktivuje se, takže se nezobrazí při spuštění **qwinsta** na příkazovém řádku.

Chcete-li tento problém vyřešit, vytvořte virtuální počítač se systémem Windows Enterprise nebo Windows Server.
1. Přejděte na [Podrobnosti o virtuálním počítači](create-host-pools-azure-marketplace.md#virtual-machine-details) a podle kroků 1-12 nastavte jednu z následujících doporučených imagí:
   - Windows 10 Enterprise s více relacemi verze 1909
   - Windows 10 Enterprise multi-session verze 1909 + Microsoft 365 Apps
   - Windows Server 2019 Datacenter
   - Windows 10 Enterprise s více relacemi verze 2004
   - Windows 10 Enterprise multi-session verze 2004 + Microsoft 365 Apps
2. Vyberte možnost **zkontrolovat a vytvořit**.

## <a name="error-name_already_registered"></a>Chyba: NAME_ALREADY_REGISTERED

Název vašeho virtuálního počítače je už zaregistrovaný a pravděpodobně je duplicitní.

Řešení tohoto problému:
1. Postupujte podle kroků v části [Odebrání hostitele relace z fondu hostitelů](#step-2-remove-the-session-host-from-the-host-pool) .
2. [Vytvořte další virtuální počítač](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal). Ujistěte se, že pro tento virtuální počítač zvolíte jedinečný název.
3. Přejít na [Azure Portal](https://portal.azure.com) a otevřít stránku **Přehled** pro fond hostitelů, ve kterém byl váš virtuální počítač. 
4. Otevřete kartu **hostitelé relace** a zkontrolujte, jestli jsou všichni hostitelé relace v daném fondu hostitelů.
5. Počkejte, až 5-10 minut, než se stav hostitele relace vysloví jako **k dispozici**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky dostupného hostitele relace](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>Vaše potíže tady nejsou uvedené ani se nevyřešily.

Pokud v tomto článku nemůžete najít problém nebo vám pokyny neudělaly, doporučujeme odinstalovat, znovu nainstalovat a znovu zaregistrovat agenta virtuálních klientů Windows. V pokynech v této části se dozvíte, jak znovu zaregistrovat virtuální počítač do služby Windows Virtual Desktop odinstalací všech agentů, spouštěcího zavaděče a komponent zásobníku, odebráním hostitele relace z fondu hostitelů, vygenerováním nového registračního klíče pro virtuální počítač a opětovnou instalací agenta a spouštěcího zavaděče. Pokud se vám na vás vztahují některé z následujících scénářů, postupujte podle těchto pokynů:
- Váš virtuální počítač se zablokuje při **upgradu** nebo **není k dispozici** .
- Váš naslouchací proces zásobníku nefunguje a pracujete ve Windows 10 1809, 1903 nebo 1909.
- Zobrazuje se chyba **EXPIRED_REGISTRATION_TOKEN**
- Nezobrazují se vaše virtuální počítače v seznamu hostitelé relací
- V okně služby se nezobrazuje **zavaděč agenta vzdálené plochy** .
- Ve Správci úloh se nezobrazuje součást **RdAgentBootLoader** .
- Od **zprostředkovatele připojení se** nedaří ověřit chybu nastavení u virtuálních počítačů vlastních imagí.
- Pokyny v tomto článku nevyřešily váš problém.

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>Krok 1: odinstalujte všechny programy agentů, spouštěcí zavaděč a součásti stacku.

Před opětovnou instalací agenta, spouštěcího zavaděče a zásobníku musíte z virtuálního počítače odinstalovat všechny existující programy komponent. Odinstalace všech programů agent, spouštěcí zavaděč a součást zásobníku:
1. Přihlaste se k VIRTUÁLNÍmu počítači jako správce. 
2. Otevřete **Ovládací panely** programy programy  >    >  **a funkce**.
3. Odeberte následující programy:
   - Zaváděcí program pro spuštění agenta vzdálené plochy
   - Agent infrastruktury služby Vzdálená plocha
   - Infrastruktura služby Vzdálená plocha – Ženeva Agent
   - Síťová sada SxS služby Vzdálená plocha
   
>[!NOTE]
>Může se zobrazit více instancí těchto programů. Nezapomeňte je odebrat.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s odinstalací programů](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>Krok 2: Odebrání hostitele relace z fondu hostitelů

Když odeberete hostitele relace z fondu hostitelů, hostitel relace již nebude registrován do tohoto fondu hostitelů. Tato možnost slouží jako resetování registrace hostitele relace. Odebrání hostitele relace z fondu hostitelů:
1. Přejít na stránku **Přehled** pro fond hostitelů, ve kterém je váš virtuální počítač, ve [Azure Portal](https://portal.azure.com). 
2. Pokud chcete zobrazit seznam všech hostitelů relací v daném fondu hostitelů, přejděte na kartu **hostitelé relace** .
3. Podívejte se na seznam hostitelé relace a vyberte virtuální počítač, který chcete odebrat.
4. Vyberte **Odebrat**.  

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky odebrání virtuálního počítače z fondu hostitelů](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>Krok 3: vygenerování nového registračního klíče pro virtuální počítač

Musíte vygenerovat nový registrační klíč, který se použije k opětovné registraci virtuálního počítače do fondu hostitelů a do služby. Pro vygenerování nového registračního klíče pro virtuální počítač:
1. Otevřete [Azure Portal](https://portal.azure.com) a pro fond hostitelů virtuálního počítače, který chcete upravit, navštivte stránku **Přehled** .
2. Vyberte **registrační klíč**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s registračním klíčem na portálu](media/reg-key.png)

3. Otevřete kartu **registrační klíč** a vyberte **generovat nový klíč**.
4. Zadejte datum vypršení platnosti a pak vyberte **OK**.  

>[!NOTE]
>Datum vypršení platnosti nemůže být menší než hodina a nesmí být delší než 27 dní od data a času generování. Důrazně doporučujeme nastavit datum vypršení platnosti na tento počet dní: 27.

5. Zkopírujte do schránky nově vygenerovaný klíč. Tento klíč budete potřebovat později.

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>Krok 4: Přeinstalujte agenta a spouštěcí zavaděč.

Po přeinstalaci aktualizované verze agenta a zaváděcího zavaděče se automaticky nainstaluje také souběžný zásobník a Agent monitorování Ženeva. Postup přeinstalace agenta a zaváděcího programu pro spouštění:
1. Přihlaste se k VIRTUÁLNÍmu počítači jako správce a použijte správnou verzi instalačního programu agenta pro vaše nasazení v závislosti na tom, která verze systému Windows váš virtuální počítač běží. Pokud máte virtuální počítač s Windows 10, postupujte podle pokynů v části [registrace virtuálních počítačů](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) ke stažení **agenta virtuálních** počítačů s Windows a **zaváděcího programu pro Windows Virtual Desktop agent**. Pokud máte virtuální počítač s Windows 7, postupujte podle kroků 13-14 v části [registrace virtuálních počítačů](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine) a Stáhněte si **agenta virtuálních** počítačů s Windows a **správce agenta virtuálních počítačů s Windows**.

   > [!div class="mx-imgBorder"]
   > ![Snímek stránky pro stažení agenta a zaváděcího programu pro spouštění](media/download-agent.png)

2. Klikněte pravým tlačítkem na instalační programy agenta a spouštěcího zavaděče, které jste stáhli.
3. Vyberte **Vlastnosti**.
4. Vyberte **odblokovat**.
5. Vyberte **OK**.
6. Spusťte instalační program agenta.
7. Až instalační program požádá o registrační token, vložte registrační klíč ze schránky. 

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky vložené registračního tokenu](media/pasted-agent-token.png)

8. Spusťte instalační program zavaděče spouštění.
9. Restartujte virtuální počítač. 
10. Přejít na [Azure Portal](https://portal.azure.com) a otevřít stránku **Přehled** pro fond hostitelů, do kterého váš virtuální počítač patří.
11. Pokud chcete zobrazit seznam všech hostitelů relací v daném fondu hostitelů, přejděte na kartu **hostitelé relace** .
12. Teď by se měl zobrazit hostitel relace registrovaný ve fondu hostitelů se stavem **k dispozici**. 

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky dostupného hostitele relace](media/hostpool-portal.png)

## <a name="next-steps"></a>Další kroky

Pokud se problém opakuje, vytvořte případ podpory a uveďte podrobné informace o problému, který máte, a všechny akce, které jste udělali, abyste se ho pokusili vyřešit. Následující seznam obsahuje další prostředky, které můžete použít k řešení problémů v nasazení virtuálních klientů Windows.

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview.md).
- Informace o řešení potíží při vytváření fondu hostitelů v prostředí virtuálních ploch Windows najdete v tématu [Vytvoření fondu prostředí a hostitele](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení potíží s klientskými připojeními k virtuální ploše Windows najdete v tématu [připojení ke službě Virtual Desktop systému Windows](troubleshoot-service-connection.md).
- Řešení potíží s klienty vzdálené plochy najdete v tématu [Poradce při potížích s klientem vzdálené plochy](troubleshoot-client.md).
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Další informace o této službě najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](../azure-resource-manager/management/view-activity-logs.md).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](../azure-resource-manager/templates/deployment-history.md).
