---
title: Řešení problémů s nasazením řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak diagnostikovat a opravovat chyby, ke kterým dochází při prvním nasazení StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 5806266955eafab8c3c8c99695ff82736de92e9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86187060"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Řešení potíží s nasazením zařízení StorSimple
## <a name="overview"></a>Přehled
Tento článek poskytuje užitečné pokyny k odstraňování potíží s nasazením Microsoft Azure StorSimple. Popisuje běžné problémy, možné příčiny a doporučené kroky, které vám pomohou vyřešit problémy, se kterými se můžete setkat při konfiguraci StorSimple. 

Tyto informace platí pro fyzické zařízení řady StorSimple 8000 i pro StorSimple Cloud Appliance.

> [!NOTE]
> Problémy týkající se konfigurace zařízení, ke kterým může dojít při prvním nasazení zařízení, nebo se můžou vyskytnout později, až bude zařízení funkční. Tento článek se zaměřuje na řešení potíží při nasazení v první době. Pokud chcete řešit potíže s provozním zařízením, přečtěte si [v tématu použití diagnostického nástroje k řešení potíží s provozním zařízením](storsimple-8000-diagnostics.md).

Tento článek také popisuje nástroje pro řešení potíží s nasazeními StorSimple a poskytuje podrobný příklad řešení potíží.

## <a name="first-time-deployment-issues"></a>Problémy s nasazením v prvním čase
Pokud narazíte na problém při prvním nasazení zařízení, vezměte v úvahu následující skutečnosti:

* Pokud řešíte řešení potíží s fyzickým zařízením, ujistěte se, že hardware je nainstalovaný a nakonfigurovaný tak, jak je popsáno v tématu [instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
* Ověřte předpoklady pro nasazení. Ujistěte se, že máte všechny informace popsané v části [Kontrolní seznam konfigurace nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Přečtěte si poznámky k verzi StorSimple a zjistěte, jestli je problém popsaný. Poznámky k verzi obsahují alternativní řešení pro známé problémy s instalací. 

Během nasazování zařízení se při spuštění Průvodce instalací a při registraci zařízení prostřednictvím Windows PowerShell pro StorSimple nejčastěji vyskytují problémy, které uživatelé čelí. (Pomocí Windows PowerShell pro StorSimple zaregistrujete a nakonfigurujete zařízení StorSimple. Další informace o registraci zařízení najdete v části [Krok 3: konfigurace a registrace zařízení prostřednictvím Windows PowerShell pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Následující části vám pomůžou vyřešit problémy, ke kterým dojde při první konfiguraci zařízení StorSimple.

## <a name="first-time-setup-wizard-process"></a>Proces Průvodce instalací v prvním čase
Následující kroky shrnují proces Průvodce instalací. Podrobné informace o instalaci najdete v tématu [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

1. Spuštěním rutiny [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) spusťte Průvodce instalací, který vás provede zbývajícími kroky. 
2. Konfigurace sítě: Průvodce instalací umožňuje konfigurovat nastavení sítě pro síťové rozhraní DATA 0 na zařízení StorSimple. Mezi tato nastavení patří následující:
   * Virtuální IP adresa (VIP), maska podsítě a brána – rutina [set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) se spustí na pozadí. Konfiguruje IP adresu, masku podsítě a bránu pro síťové rozhraní DATA 0 na zařízení StorSimple.
   * Primární server DNS – rutina [set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) se spustí na pozadí. Nakonfiguruje nastavení DNS pro řešení StorSimple.
   * Server NTP – rutina [set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) se spustí na pozadí. Konfiguruje nastavení serveru NTP pro řešení StorSimple.
   * Volitelný webový proxy server – rutina [set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) se spustí na pozadí. Nastavuje a povoluje konfiguraci webového proxy serveru pro řešení StorSimple.
3. Nastavení hesla: dalším krokem je nastavení hesla správce zařízení.
   Heslo správce zařízení se používá pro přihlášení k vašemu zařízení. Výchozí heslo zařízení je **Password1**.
        
     > [!IMPORTANT]
     > Hesla se shromažďují před registrací, ale aplikují se jenom po úspěšné registraci zařízení. Pokud dojde k chybě při použití hesla, budete vyzváni k zadání hesla znovu, dokud nebudou shromažďována požadovaná hesla (která splňují požadavky na složitost).
     
4. Registrace zařízení: v posledním kroku zaregistrujete zařízení ve službě StorSimple Device Manager spuštěné v Microsoft Azure. Registrace vyžaduje, abyste z Azure Portal [získali registrační klíč služby](storsimple-8000-manage-service.md#get-the-service-registration-key) a poskytovali ho v Průvodci instalací nástroje. **Po úspěšné registraci zařízení je k dispozici šifrovací klíč dat služby. Nezapomeňte tento šifrovací klíč uchovávat v bezpečném umístění, protože bude nutné zaregistrovat všechna následná zařízení se službou.**

## <a name="common-errors-during-device-deployment"></a>Běžné chyby při nasazování zařízení
V následujících tabulkách jsou uvedeny běžné chyby, se kterými se můžete setkat:

* Nakonfigurujte požadovaná nastavení sítě.
* Nakonfigurujte volitelná nastavení webového proxy serveru.
* Nastavte heslo správce zařízení.
* Zaregistrujte zařízení.

## <a name="errors-during-the-required-network-settings"></a>Chyby při požadovaném nastavení sítě
| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Tento příkaz lze spustit pouze na aktivním řadiči. |V pasivním řadiči se provedla konfigurace. |Spusťte tento příkaz z aktivního řadiče. Další informace najdete v tématu [určení aktivního řadiče na zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: zařízení není připravené. |Nastaly problémy s připojením k síti u DATA 0. |Ověřte připojení fyzické sítě k datům 0. |
| 3 |Invoke-HcsSetupWizard: došlo ke konfliktu IP adres s jiným systémem v síti (výjimka z HRESULT: 0x80070263). |IP adresa zadaná pro DATA 0 již byla používána jiným systémem. |Zadejte novou IP adresu, která se nepoužívá. |
| 4 |Invoke-HcsSetupWizard: prostředek clusteru se nezdařil. (Výjimka z HRESULT: 0x800713AE). |Duplicitní VIP. Zadaná IP adresa je již používána. |Zadejte novou IP adresu, která se nepoužívá. |
| 5 |Invoke-HcsSetupWizard: Neplatná adresa IPv4. |IP adresa je k dispozici v nesprávném formátu. |Ověřte formát a znovu zadejte svoji IP adresu. Další informace najdete v tématu [adresování IPv4][1]. |
| 6 |Invoke-HcsSetupWizard: Neplatná adresa IPv6. |IP adresa je k dispozici v nesprávném formátu. |Ověřte formát a znovu zadejte svoji IP adresu. Další informace najdete v tématu [adresování IPv6][2]. |
| 7 |Invoke-HcsSetupWizard: z mapovače koncových bodů nejsou k dispozici žádné další koncové body. (Výjimka z HRESULT: 0x800706D9) |Funkce clusteru nefunguje. |Pro další kroky [kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) . |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Chyby při nastavení volitelného webového proxy serveru
| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: neplatný parametr (výjimka z HRESULT: 0x80070057) |Jeden z parametrů poskytnutých pro nastavení proxy serveru není platný. |Identifikátor URI není k dispozici ve správném formátu. Použijte následující formát: http:// *\<IP address or FQDN of the web proxy server>* :*\<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: Server RPC není k dispozici (výjimka z HRESULT: 0x800706ba). |Hlavní příčinou je jedna z následujících:<ol><li>Cluster není zapnutý.</li><li>Pasivní kontroler nemůže komunikovat s aktivním řadičem a příkaz se spustí z pasivního kontroleru.</li></ol> |V závislosti na hlavní příčině:<ol><li>[Kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) , abyste se ujistili, že cluster je v clusteru.</li><li>Spusťte příkaz z aktivního řadiče. Pokud chcete spustit příkaz z pasivního kontroleru, budete muset zajistit, aby pasivní řadič mohl komunikovat s aktivním řadičem. Pokud je toto připojení přerušeno, budete muset [kontaktovat podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) .</li></ol> |
| 3 |Invoke-HcsSetupWizard: RPC – neúspěšné volání (výjimka z HRESULT: 0X800706be) |Cluster je mimo provoz. |[Kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) , abyste se ujistili, že cluster je v clusteru. |
| 4 |Invoke-HcsSetupWizard: prostředek clusteru se nenašel (výjimka z HRESULT: 0x8007138f). |Prostředek clusteru se nenašel. K tomu může dojít v případě, že instalace nebyla správná. |Možná budete muset zařízení obnovit do výchozího továrního nastavení. Pokud chcete vytvořit prostředek clusteru, obraťte se na [Podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) . |
| 5 |Invoke-HcsSetupWizard: prostředek clusteru není online (výjimka z HRESULT: 0x8007138c) |Prostředky clusteru nejsou online. |Pro další kroky [kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) . |

## <a name="errors-related-to-device-administrator-password"></a>Chyby související s heslem správce zařízení
Výchozí heslo správce zařízení je **Heslo1**. Platnost hesla vyprší po prvním přihlášení; Proto budete muset použít Průvodce instalací k jeho změně. Při prvním registraci zařízení musíte zadat nové heslo správce zařízení. 

Ujistěte se, že hesla splňují následující požadavky:

* Heslo správce zařízení by mělo mít délku 8 až 15 znaků.
* Hesla by měla obsahovat 3 z následujících 4 typů znaků: malá písmena, Velká písmena, číslice a speciální. 
* Heslo nemůže být stejné jako posledních 24 hesel.

Kromě toho mějte na paměti, že platnost hesla vyprší každý rok a můžete ji změnit až po úspěšné registraci zařízení. Pokud se registrace z jakéhokoli důvodu nezdaří, hesla se nezmění.

Pokud chcete získat další informace o hesle Správce zařízení, přejděte na adresu [použití služby StorSimple Device Manager ke změně hesla StorSimple](storsimple-8000-change-passwords.md).

Při nastavování správce zařízení a StorSimple hesla Snapshot Manager se může zobrazit jedna nebo více následujících chyb.

| Ne. | Chybová zpráva | Doporučená akce |
| --- | --- | --- |
| 1 |Heslo překračuje maximální délku. |Heslo správce zařízení musí mít délku 8 až 15 znaků. |
| 2 |Heslo nesplňuje požadovanou délku. |Heslo správce zařízení musí mít délku 8 až 15 znaků.|
| 3 |Heslo musí obsahovat jenom malá písmena. |Hesla musí obsahovat 3 z následujících 4 typů znaků: malá písmena, Velká písmena, číslice a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 4 |Heslo musí obsahovat číselné znaky. |Hesla musí obsahovat 3 z následujících 4 typů znaků: malá písmena, Velká písmena, číslice a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 5 |Heslo musí obsahovat speciální znaky. |Hesla musí obsahovat 3 z následujících 4 typů znaků: malá písmena, Velká písmena, číslice a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 6 |Heslo musí obsahovat 3 z následujících 4 typů znaků: velká písmena, malá písmena, číslice a speciální. |Heslo neobsahuje požadované typy znaků. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 7 |Parametr neodpovídá potvrzení. |Ujistěte se, že vaše heslo splňuje všechny požadavky a že jste ho zadali správně. |
| 8 |Heslo se nemůže shodovat s výchozím nastavením. |Výchozí heslo je *Heslo1*. Po prvním přihlášení budete muset toto heslo změnit. |
| 9 |Heslo, které jste zadali, se neshoduje s heslem zařízení. Zadejte prosím heslo znovu. |Ověřte heslo a znovu ho zadejte. |

Hesla se shromažďují předtím, než se zařízení zaregistruje, ale uplatní se až po úspěšné registraci. Pracovní postup obnovení hesla vyžaduje, aby bylo zařízení zaregistrováno.

> [!IMPORTANT]
> Obecně platí, že pokud se pokus o použití hesla nezdaří, pak se software opakovaně pokusí o shromáždění hesla, dokud nebude úspěšný. Ve výjimečných případech nelze heslo použít. V takovém případě můžete zařízení zaregistrovat a pokračovat, ale hesla se nezmění. Po registraci z Azure Portal můžete změnit heslo správce zařízení.


Heslo můžete v Azure Portal resetovat prostřednictvím služby Device Manager StorSimple. Další informace získáte, když přejdete na [změnit heslo správce zařízení](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Chyby při registraci zařízení
K registraci zařízení použijete službu StorSimple Device Manager spuštěnou v Microsoft Azure. Při registraci zařízení se můžete setkat s jedním nebo několika následujícími problémy.

| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Chyba 350027: Nepodařilo se zaregistrovat zařízení s Device Manager StorSimple. | |Počkejte několik minut a potom operaci opakujte. Pokud se problém nevyřeší, obraťte se na [Podpora Microsoftu](storsimple-8000-contact-microsoft-support.md). |
| 2 |Chyba 350013: při registraci zařízení došlo k chybě. Příčinou může být nesprávný registrační klíč služby. | |Zaregistrujte prosím zařízení znovu se správným registračním klíčem služby. Další informace najdete v tématu [získání registračního klíče služby.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Chyba 350063: předala se ověření do služby StorSimple Device Manager, ale registrace se nezdařila. Zkuste prosím tuto operaci za chvíli znovu. |Tato chyba označuje, že ověřování pomocí služby ACS bylo úspěšné, ale nezdařila se registrace volání služby. Může to být důsledek občasné síťové porucha. |Pokud se problém nevyřeší, [obraťte](storsimple-8000-contact-microsoft-support.md)se prosím na podpora Microsoftu. |
| 4 |Chyba 350049: Služba nemohla být během registrace dostupná. |Po volání služby se přijme výjimka webu. V některých případech to může být opraveno opakováním operace později. |Zkontrolujte prosím svoji IP adresu a název DNS a potom zkuste operaci zopakovat. Pokud potíže potrvají, [obraťte se na podpora Microsoftu.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Chyba 350031: zařízení je už zaregistrované. | |Žádná akce není nutná. |
| 6 |Chyba 350016: registrace zařízení se nezdařila. | |Ujistěte se prosím, že registrační klíč je správný. |
| 7 |Invoke-HcsSetupWizard: při registraci zařízení došlo k chybě. Příčinou může být nesprávná IP adresa nebo název DNS. Zkontrolujte prosím nastavení sítě a zkuste to znovu. Pokud potíže potrvají, [obraťte](storsimple-8000-contact-microsoft-support.md)se na podpora Microsoftu. (Chyba 350050) |Ujistěte se, že vaše zařízení může testovat test přes síť mimo síť. Pokud nemáte připojení k vnější síti, může při této chybě dojít k selhání registrace. Tato chyba může být kombinací jedné nebo více z následujících možností:<ul><li>Nesprávná IP adresa</li><li>Nesprávná podsíť</li><li>Nesprávná brána</li><li>Nesprávná nastavení DNS</li></ul> |Projděte si postup popsaný v tématu [krok za krokem – příklad řešení potíží](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: aktuální operace selhala kvůli vnitřní chybě služby [0x1FBE2]. Po nějaké době zkuste operaci zopakovat. Pokud se problém nevyřeší, obraťte se prosím na podpora Microsoftu. |Jedná se o obecnou chybu vyvolanou pro všechny neviditelné chyby uživatele ze služby nebo agenta. Nejběžnějším důvodem může být to, že ověřování ACS selhalo. Možnou příčinou selhání je to, že dochází k problémům s konfigurací serveru NTP a čas v zařízení není správně nastavený. |Opravte čas (Pokud dojde k problémům) a pak zkuste operaci registrace zopakovat. Použijete-li příkaz Set-HcsSystem-TimeZone pro úpravu časového pásma, Velká písmena jednotlivých slov v časovém pásmu (například Tichomoří (běžný čas)).  Pokud s tím budou dál problémy, [kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) pro další kroky. |
| 9 |Upozornění: nepovedlo se aktivovat zařízení. Hesla správce zařízení a StorSimple Snapshot Manager hesla se nezměnila. |Pokud se registrace nepovede, hesla správce zařízení a StorSimple Snapshot Manager se nezmění. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Nástroje pro řešení potíží s nasazeními StorSimple
StorSimple zahrnuje několik nástrojů, které můžete použít k řešení potíží s řešením StorSimple. Tady jsou některé z nich:

* Podporuje balíčky a protokoly zařízení.
* Rutiny navržené speciálně pro řešení potíží.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Balíčky podpory a protokoly zařízení, které jsou k dispozici pro řešení potíží
Balíček pro podporu obsahuje všechny příslušné protokoly, které mohou pomoct týmu podpora Microsoftu s řešením potíží se zařízeními. Pomocí Windows PowerShell pro StorSimple můžete vygenerovat zašifrovaný balíček pro podporu, který pak můžete sdílet s pracovníky podpory.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Zobrazení protokolů nebo obsahu balíčku pro podporu
1. Použijte Windows PowerShell pro StorSimple k vygenerování balíčku pro podporu, jak je popsáno v tématu [Vytvoření a Správa balíčku pro podporu](storsimple-8000-create-manage-support-package.md).
2. Stáhněte si [dešifrovací skript](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) místně na klientském počítači.
3. Pomocí tohoto [podrobného postupu](storsimple-8000-create-manage-support-package.md#edit-a-support-package) otevřete a dešifrujte balíček pro podporu.
4. Protokoly dešifrovaných balíčků podpory jsou ve formátu ETW/etvx. K zobrazení těchto souborů ve Windows Prohlížeč událostí můžete použít následující postup:
   
   1. Spusťte na svém klientovi Windows příkaz **eventvwr** . Tím se spustí Prohlížeč událostí.
   2. V podokně **Akce** klikněte na možnost **Otevřít uložený protokol** a ukažte na soubory protokolu ve formátu etvx/ETW (balíček pro podporu). Teď si můžete soubor zobrazit. Po otevření souboru můžete kliknout pravým tlačítkem a uložit soubor jako text.
      
      > [!IMPORTANT]
      > K otevření těchto souborů v prostředí Windows PowerShell můžete použít také rutinu **GET-WinEvent** . Další informace najdete v tématu [GET-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) v referenční dokumentaci k rutinám Windows PowerShellu.
     
5. Když se protokoly otevřou v Prohlížeč událostí, vyhledejte následující protokoly, které obsahují problémy související s konfigurací zařízení:
   
   * hcs_pfconfig protokol/Operational
   * hcs_pfconfig/config
6. V souborech protokolu vyhledejte řetězce související s rutinami, které jsou volány Průvodcem instalací nástroje. Seznam těchto rutin najdete v tématu [proces průvodce nastavením prvního času](#first-time-setup-wizard-process) .
7. Pokud se vám nedaří zjistit příčinu problému, můžete [kontaktovat podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) pro další kroky. Postupujte podle kroků v části [Vytvoření žádosti o podporu](storsimple-8000-contact-microsoft-support.md#create-a-support-request) při kontaktování podpora Microsoftu s žádostí o pomoc.

## <a name="cmdlets-available-for-troubleshooting"></a>Dostupné rutiny pro řešení potíží
K detekci chyb připojení použijte následující rutiny Windows PowerShellu.

* `Get-NetAdapter`: Tuto rutinu použijte k detekci stavu síťových rozhraní.
* `Test-Connection`: Pomocí této rutiny můžete ověřit síťové připojení v síti i mimo ni.
* `Test-HcsmConnection`: Pomocí této rutiny můžete ověřit připojení zařízení, které se úspěšně zaregistrovalo.
* `Sync-HcsTime`: Tuto rutinu použijte k zobrazení času zařízení a vynucení synchronizace času se serverem NTP.
* `Enable-HcsPing` a `Disable-HcsPing` : Tyto rutiny použijte, pokud chcete hostitelům dovolit testovat síťová rozhraní na zařízení StorSimple. Ve výchozím nastavení nereagují síťová rozhraní StorSimple na požadavky příkazů k zadání požadavku.
* `Trace-HcsRoute`: Tuto rutinu použijte jako nástroj pro trasování tras. Odesílá pakety do každého směrovače způsobem až do konečného cíle v časovém intervalu a pak vypočítá výsledky na základě paketů vrácených z jednotlivých segmentů. Vzhledem `Trace-HcsRoute` k tomu, že se v jakémkoli směrovači nebo propojení zobrazuje stupeň ztrát paketů, můžete určit, které směrovače nebo odkazy můžou způsobovat problémy se sítí.
* `Get-HcsRoutingTable`: Pomocí této rutiny můžete zobrazit tabulku směrování místní IP adresy.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Řešení potíží pomocí rutiny Get-NetAdapter
Když nakonfigurujete síťová rozhraní pro nasazení na první zařízení, stav hardwaru není dostupný v uživatelském rozhraní služby StorSimple Device Manager, protože zařízení ještě není zaregistrované ve službě. Kromě toho okno **stav hardwaru** nemusí vždy správně odrážet stav zařízení, zejména v případě, že dochází k problémům, které mají vliv na synchronizaci služby. V těchto situacích můžete použít `Get-NetAdapter` rutinu k určení stavu a stavu síťových rozhraní.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Zobrazení seznamu všech síťových adaptérů v zařízení
1. Spusťte Windows PowerShell pro StorSimple a potom zadejte `Get-NetAdapter` . 
2. Použijte výstup `Get-NetAdapter` rutiny a následující pokyny pro pochopení stavu síťového rozhraní.
   
   * Pokud je rozhraní v pořádku a povoleno, **zobrazí se stav** **ifIndex** .
   * Pokud je rozhraní v pořádku, ale není fyzicky připojené (síťovým kabelem), **ifIndex** se zobrazuje jako **zakázaný**.
   * Pokud je rozhraní v pořádku, ale není povoleno, zobrazí se stav **ifIndex** jako **NotPresent**.
   * Pokud rozhraní neexistuje, nezobrazí se v tomto seznamu. Uživatelské rozhraní služby StorSimple Device Manager bude toto rozhraní pořád zobrazovat ve stavu selhání.

Další informace o tom, jak používat tuto rutinu, najdete v tématu rutiny [Get-netadapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) v tématu Reference k rutinám prostředí Windows PowerShell.

V následujících částech jsou uvedeny ukázky výstupu z `Get-NetAdapter` rutiny.

 V těchto ukázkách byl řadič 0 pasivním kontrolérem a byl nakonfigurován následujícím způsobem:

* V zařízení existovala síťová rozhraní data 0, DATA 1, DATA 2 a DATA 3.
* Síťové adaptéry DATA 4 a DATA 5 nejsou k dispozici. Proto nejsou uvedené ve výstupu.
* DATA 0 byla povolena.

Řadič 1 byl aktivním řadičem a byl nakonfigurován následujícím způsobem:

* DATA 0, DATA 1, DATA 2, DATA 3, data 4 a síťová rozhraní DATA 5 v zařízení existovala.
* DATA 0 byla povolena.

**Vzorový výstup – Controller 0**

Následuje výstup z kontroleru 0 (pasivní kontroler). DATA 1, DATA 2 a DATA 3 nejsou připojená. DATA 4 a DATA 5 nejsou v seznamu uvedená, protože se na zařízení nevyskytují.

```output
Controller0>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
Ethernet 2           HCS VNIC                                    13       Up
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
```


**Vzorový výstup – Controller 1**

Následuje výstup z kontroleru 1 (aktivní kontroler). V zařízení je nakonfigurované a funkční jenom síťové rozhraní DATA 0.

```output
Controller1>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
Ethernet 2           HCS VNIC                                    13       Up
DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent
```


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Řešení potíží pomocí rutiny Test-Connection
Pomocí `Test-Connection` rutiny můžete určit, jestli se zařízení StorSimple může připojit k vnější síti. Pokud jsou v Průvodci instalací správně nakonfigurované všechny parametry sítě, včetně DNS, můžete pomocí `Test-Connection` rutiny testovat známou adresu mimo síť, například Outlook.com.

Pokud je parametr pøíkazového testu zakázaný, měli byste povolit příkazy pro řešení potíží s připojením pomocí příkazů if.

Podívejte se na následující ukázky výstupu z `Test-Connection` rutiny.

> [!NOTE]
> V první ukázce je zařízení nakonfigurované s nesprávným DNS. Ve druhé ukázce je DNS správný.

**Ukázkový výstup – nesprávná služba DNS**

V následující ukázce není k dispozici výstup adres IPV4 a IPV6, což znamená, že server DNS není přeložen. To znamená, že není k dispozici připojení k vnější síti a je nutné zadat správný server DNS.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
```

**Ukázkový výstup – správné DNS**

V následující ukázce DNS vrátí adresu IPV4, což značí, že služba DNS je nakonfigurovaná správně. Tím se potvrdí, že existuje připojení k vnější síti.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
```

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Řešení potíží pomocí rutiny Test-HcsmConnection
Použijte `Test-HcsmConnection` rutinu pro zařízení, které už je připojené k vaší službě StorSimple Device Manager a je zaregistrované. Tato rutina vám pomůže ověřit připojení mezi registrovaným zařízením a odpovídající službou StorSimple Device Manager. Tento příkaz můžete spustit na Windows PowerShell pro StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Spuštění rutiny Test-HcsmConnection
1. Ujistěte se, že je zařízení zaregistrované.
2. Ověřte stav zařízení. Pokud dojde k deaktivaci zařízení v režimu údržby nebo offline, může se zobrazit jedna z následujících chyb:
   
   * ErrorCode. CiSDeviceDecommissioned – označuje, že zařízení je deaktivováno.
   * ErrorCode. DeviceNotReady – označuje, že zařízení je v režimu údržby.
   * ErrorCode. DeviceNotReady – označuje, že zařízení není online.
3. Ověřte, že je spuštěná služba StorSimple Device Manager (použijte rutinu [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) ). Pokud služba není spuštěná, může se zobrazit následující chyby:
   
   * ErrorCode. CiSApplianceAgentNotOnline
   * ErrorCode. CisPowershellScriptHcsError – to znamená, že při spuštění Get-ClusterResource došlo k výjimce.
4. Ověřte token Access Control Service (ACS). Pokud vyvolá výjimku z webu, může to být výsledkem problému brány, chybějícího ověřování proxy serveru, nesprávné služby DNS nebo selhání ověřování. Můžou se zobrazit následující chyby:
   
   * ErrorCode. CiSApplianceGateway – označuje výjimku HttpStatusCode. BadGateway: službě překladu názvů se nepovedlo přeložit název hostitele.
   * ErrorCode. CiSApplianceProxy – označuje výjimku HttpStatusCode. ProxyAuthenticationRequired (kód stavu HTTP 407): klienta se nepovedlo ověřit pomocí proxy server.
   * ErrorCode. CiSApplianceDNSError – označuje výjimku WebExceptionStatus rovným. NameResolutionFailure: službě překladu názvů se nepovedlo přeložit název hostitele.
   * ErrorCode. CiSApplianceACSError – to znamená, že služba vrátila chybu ověřování, ale existuje připojení.
     
     Pokud nevyvolá výjimku webu, vyhledejte kód ErrorCode. CiSApplianceFailure. To znamená, že zařízení selhalo.
5. Ověřte připojení ke cloudové službě. Pokud služba vyvolá výjimku webu, mohou se zobrazit následující chyby:
   
   * ErrorCode. CiSApplianceGateway – označuje výjimku HttpStatusCode. BadGateway: zprostředkující proxy server obdržela chybný požadavek od jiného proxy serveru nebo z původního serveru.
   * ErrorCode. CiSApplianceProxy – označuje výjimku HttpStatusCode. ProxyAuthenticationRequired (kód stavu HTTP 407): klienta se nepovedlo ověřit pomocí proxy server.
   * ErrorCode. CiSApplianceDNSError – označuje výjimku WebExceptionStatus rovným. NameResolutionFailure: službě překladu názvů se nepovedlo přeložit název hostitele.
   * ErrorCode. CiSApplianceACSError – to znamená, že služba vrátila chybu ověřování, ale existuje připojení.
     
     Pokud nevyvolá výjimku webu, vyhledejte kód ErrorCode. CiSApplianceSaasServiceError. To indikuje problém se službou StorSimple Device Manager.
6. Ověřte Azure Service Bus připojení. ErrorCode. CiSApplianceServiceBusError indikuje, že se zařízení nemůže připojit k Service Bus.

Soubory protokolu CiSCommandletLog0Curr. errlog a CiSAgentsvc0Curr. errlog budou mít další informace, například Podrobnosti o výjimce.

Další informace o tom, jak použít rutinu, najdete v referenční dokumentaci prostředí Windows PowerShell v tématu [test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) .

> [!IMPORTANT]
> Tuto rutinu můžete spustit pro aktivní i pasivní kontroler.

Podívejte se na následující ukázky výstupu z `Test-HcsmConnection` rutiny.

**Ukázkový výstup – úspěšně registrované zařízení s StorSimple Update 3**

```output
Controller1>Test-HcsmConnection

Checking device registration state  ... Success
Device registered successfully

Checking primary NTP server [time.windows.com] ... Success

Checking web proxy  ... NOT SET

Checking primary IPv4 DNS server [10.222.118.154] ... Success
Checking primary IPv6 DNS server  ... NOT SET
Checking secondary IPv4 DNS server [10.222.120.24] ... Success
Checking secondary IPv6 DNS server  ... NOT SET

Checking device online  ... Success

Checking device authentication  ... This will take a few minutes.
Checking device authentication  ... Success

Checking connectivity from device to service  ... This will take a few minutes.

Checking connectivity from device to service  ... Success

Checking connectivity from service to device  ... Success

Checking connectivity to Microsoft Update servers  ... Success
Controller1>
```

**Ukázkový výstup – offline zařízení** 

Tato ukázka je ze zařízení, které má ve Azure Portal stav **offline** .

```output
Checking device registrationstate: Success
Device is registered successfully
Checking connectivity from device to SaaS.. Failure
```

Zařízení se nemohlo připojit pomocí aktuální konfigurace webového proxy serveru. Může to být problém s konfigurací webového proxy serveru nebo problémem s připojením k síti. V takovém případě byste měli zkontrolovat, jestli je vaše nastavení webového proxy serveru správné a jestli jsou webové proxy servery online a dostupné.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Řešení potíží pomocí rutiny Sync-HcsTime
Pomocí této rutiny můžete zobrazit čas zařízení. Pokud má čas zařízení posun se serverem NTP, můžete tuto rutinu použít k vynucení synchronizace času se serverem NTP.
- Pokud je posun mezi zařízením a serverem NTP delší než 5 minut, zobrazí se upozornění. 
- Pokud posun přesáhne 15 minut, zařízení přejde do režimu offline. Tuto rutinu můžete přesto použít k vynucení synchronizace času. 
- Pokud však posun přesáhne 15 hodin, nebudete moci vynutit synchronizaci času a zobrazí se chybová zpráva.

**Ukázkový výstup – vynucená synchronizace pomocí Sync-HcsTime**

```output
Controller0>Sync-HcsTime
The current device time is 4/24/2015 4:05:40 PM UTC.

Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
[Y] Yes [N] No (Default is "Y"): Y
Controller0>
```

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Řešení potíží pomocí rutin Enable-HcsPing a Disable-HcsPing
Pomocí těchto rutin se ujistěte, že síťová rozhraní v zařízení reagují na požadavky na příkazy protokolu ICMP. Ve výchozím nastavení nereagují síťová rozhraní StorSimple na požadavky příkazů k zadání požadavku. Použití této rutiny je nejjednodušší způsob, jak zjistit, jestli je zařízení online a dostupné.

**Vzorový výstup – Enable-HcsPing a Disable-HcsPing**

```output
Controller0>
Controller0>Enable-HcsPing
Successfully enabled ping.
Controller0>
Controller0>
Controller0>Disable-HcsPing
Successfully disabled ping.
Controller0>
```

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Řešení potíží pomocí rutiny Trace-HcsRoute
Tuto rutinu použijte jako nástroj pro trasování tras. Odesílá pakety do každého směrovače způsobem až do konečného cíle v časovém intervalu a pak vypočítá výsledky na základě paketů vrácených z jednotlivých segmentů. Vzhledem k tomu, že rutina zobrazuje stupeň ztrát paketů na jakémkoli směrovači nebo propojení, můžete určit, které směrovače nebo odkazy můžou způsobovat problémy se sítí.

**Ukázkový výstup ukazující postup trasování trasy paketů pomocí Trace-HcsRoute**

```output
Controller0>Trace-HcsRoute -Target 10.126.174.25

Tracing route to contoso.com [10.126.174.25]
over a maximum of 30 hops:
   0  HCSNode0 [10.126.173.90]
   1  contoso.com [10.126.174.25]

Computing statistics for 25 seconds...
            Source to Here   This Node/Link
Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
   0                                           HCSNode0 [10.126.173.90]
                                 0/ 100 =  0%   |
   1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
[10.126.174.25]

Trace complete.
```

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Řešení potíží pomocí rutiny Get-HcsRoutingTable
Pomocí této rutiny můžete zobrazit směrovací tabulku pro zařízení StorSimple. Směrovací tabulka je sada pravidel, která vám pomůžou určit, kde budou směrovány datové pakety přenášené přes Internet Protocol (IP) síť.

Tabulka směrování zobrazuje rozhraní a bránu, která směruje data do zadaných sítí. Poskytuje také metriku směrování, která je tvůrcem rozhodnutí pro cestu pořízenou s cílem dosáhnout určitého cíle. Čím nižší je metrika směrování, tím vyšší je priorita.

Například pokud máte 2 síťová rozhraní, DATA 2 a DATA 3, připojená k Internetu. Pokud metriky směrování pro DATA 2 a DATA 3 jsou 15 a 261, pak jsou DATA 2 s nižší metrikou směrování upřednostňovanou rozhraním, které se používá k přístupu k Internetu.

Pokud na zařízení StorSimple používáte Update 1, vaše síťové rozhraní DATA 0 má nejvyšší prioritu cloudového provozu. To znamená, že i v případě, že existují jiná cloudová rozhraní, přenos do cloudu by byl směrován přes DATA 0.

Pokud spustíte `Get-HcsRoutingTable` rutinu bez zadání parametrů (jak ukazuje následující příklad), rutina vytvoří výstup směrovacích tabulek IPv4 i IPv6. Případně můžete zadat `Get-HcsRoutingTable -IPv4` nebo `Get-HcsRoutingTable -IPv6`  pro získání příslušné směrovací tabulky.

```output
Controller0>
Controller0>Get-HcsRoutingTable
===========================================================================
Interface List
   14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
   12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
   13...28 18 78 bc 4b 85 ......HCS VNIC
   1...........................Software Loopback Interface 1
   21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
   22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
         127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
         127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
   127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
      169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
   169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
      192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
   192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
   192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
         224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
         224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
         224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
   255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
   255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
   255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
===========================================================================
Persistent Routes:
   Network Address          Netmask  Gateway Address  Metric
            0.0.0.0          0.0.0.0  192.168.111.100       5
===========================================================================

IPv6 Route Table
===========================================================================
Active Routes:
   If Metric Network Destination      Gateway
   1    306 ::1/128                  On-link
   13    276 fd99:4c5b:5525:d80b::/64 On-link
   13    276 fd99:4c5b:5525:d80b::1/128
                                    On-link
   13    276 fd99:4c5b:5525:d80b::3/128
                                    On-link
   13    276 fe80::/64                On-link
   12    261 fe80::/64                On-link
   13    276 fe80::17a:4eba:7c80:727f/128
                                    On-link
   12    261 fe80::fc97:1a53:e81a:3454/128
                                    On-link
   1    306 ff00::/8                 On-link
   13    276 ff00::/8                 On-link
   12    261 ff00::/8                 On-link
   14    266 ff00::/8                 On-link
===========================================================================
Persistent Routes:
   None

Controller0>
```

## <a name="step-by-step-storsimple-troubleshooting-example"></a>StorSimple příklad řešení potíží s podrobnými postupy
Následující příklad ukazuje podrobný Poradce při potížích s nasazením StorSimple. V ukázkovém scénáři se registrace zařízení nezdařila s chybovou zprávou oznamující, že nastavení sítě nebo název DNS nejsou správné.

Vrácená chybová zpráva je:

```output
Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
+CategoryInfo: Not specified
+FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand
```

Chyba může být způsobena některou z následujících příčin:

* Nesprávná instalace hardwaru
* Chybná síťová rozhraní
* Nesprávná IP adresa, maska podsítě, brána, primární server DNS nebo webový proxy server
* Nesprávný registrační klíč
* Nesprávná nastavení brány firewall

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Vyhledání a oprava potíží s registrací zařízení
1. Ověřte konfiguraci zařízení: na aktivním řadiči spusťte příkaz `Invoke-HcsSetupWizard` .
   
   > [!NOTE]
   > Průvodce instalací nástroje musí běžet na aktivním řadiči. Pokud chcete ověřit, jestli jste připojení k aktivnímu řadiči, podívejte se na banner prezentovaný v konzole sériového portu. Banner označuje, zda jste připojeni k řadiči 0 nebo řadiči 1 a zda je řadič aktivní nebo pasivní. Další informace najdete [v poznámkách k identifikaci aktivního řadiče na zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Ujistěte se, že je zařízení správně zapojené: Zkontrolujte síťové kabely na zadní rovině zařízení. Kabely jsou specifické pro model zařízení. Další informace najdete v [poznámkách k instalaci zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo o [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Pokud používáte 10 GbE síťových portů, budete muset použít poskytnuté adaptéry QSFP-SFP a kabely SFP. Další informace najdete v [seznamu kabelů, přepínačů a vysílačů doporučených pro porty 10 GbE](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Ověřte stav síťového rozhraní:
   
   * K detekci stavu síťových rozhraní pro DATA 0 použijte rutinu Get-NetAdapter. 
   * Pokud odkaz nefunguje, stav **ifindex** oznámí, že rozhraní je mimo provoz. Pak bude potřeba ověřit síťové připojení portu k zařízení a přepínači. Budete taky muset vyfiltrovat chybné kabely. 
   * Pokud máte podezření, že se port DATA 0 na aktivním řadiči nezdařil, můžete to ověřit tak, že se připojíte k portu DATA 0 na řadiči 1. Potvrďte to tak, že odpojíte síťový kabel od back-of zařízení od řadiče 0, připojíte kabel k řadiči 1 a znovu spustíte rutinu Get-NetAdapter.
     Pokud port s daty 0 na řadiči selhává, [kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) pro další kroky. Možná budete muset tento kontroler v systému nahradit.
4. Ověřte připojení k přepínači:
   
   * Ujistěte se, že síťová rozhraní DATA 0 na řadiči 0 a řadiči 1 v primární skříni jsou ve stejné podsíti. 
   * Ověřte centrum nebo směrovač. Obvykle byste měli oba řadiče připojit ke stejnému rozbočovači nebo směrovači. 
   * Ujistěte se, že přepínače používané pro připojení mají DATA 0 pro oba řadiče ve stejné síti vLAN.
5. Eliminujte všechny chyby uživatele:
   
   * Spusťte znovu Průvodce instalací (spusťte **Invoke-HcsSetupWizard**) a znovu zadejte hodnoty, abyste se ujistili, že nejsou k dispozici žádné chyby. 
   * Ověřte použití registračního klíče. Stejný registrační klíč se dá použít k připojení několika zařízení ke službě StorSimple Device Manager. Použijte postup v části [získání registračního klíče služby](storsimple-8000-manage-service.md#get-the-service-registration-key) , abyste měli jistotu, že používáte správný registrační klíč.
     
     > [!IMPORTANT]
     > Pokud máte spuštěné více služeb, budete muset zajistit, aby se registrační klíč pro příslušnou službu použil k registraci zařízení. Pokud jste zaregistrovali zařízení s nesprávnou Device Managerovou službou StorSimple, budete muset [kontaktovat podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) pro další kroky. Možná budete muset provést obnovení továrního nastavení zařízení (což by mohlo způsobit ztrátu dat) a pak ho připojit k zamýšlené službě.
     > 
     > 
6. Pomocí rutiny Test-Connection ověřte, zda máte připojení k vnější síti. Další informace najdete v tématu [řešení potíží pomocí rutiny Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Kontroluje rušení brány firewall. Pokud jste ověřili správnost nastavení virtuální IP adresy (VIP), podsítě, brány a DNS a pořád vidíte problémy s připojením, pak je možné, že brána firewall blokuje komunikaci mezi zařízením a vnější sítí. Musíte zajistit, aby porty 80 a 443 byly na zařízení StorSimple k dispozici pro odchozí komunikaci. Další informace najdete v tématu [požadavky na síť pro zařízení StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Podívejte se na protokoly. Přejít na [podpůrné balíčky a protokoly zařízení, které jsou k dispozici pro řešení potíží](#support-packages-and-device-logs-available-for-troubleshooting).
9. Pokud předchozí kroky problém nevyřeší, obraťte se na [Podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) s žádostí o pomoc.

## <a name="next-steps"></a>Další kroky
[Přečtěte si, jak pomocí diagnostického nástroje řešit problémy se zařízením s StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
