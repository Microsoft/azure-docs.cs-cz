---
title: Řešení potíží s vyhrazeným modulem HSM – vyhrazený modul zabezpečení Azure | Microsoft Docs
description: Přehled vyhrazeného modulu HARDWAROVÉho zabezpečení Azure poskytuje klíčové možnosti úložiště v Azure, které vyhovují certifikaci FIPS 140-2 Level 3.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: b911181abea06894873e64da51afbb8799f1066a
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927834"
---
# <a name="troubleshooting-the-azure-dedicated-hsm-service"></a>Řešení potíží s vyhrazenou službou HSM Azure

Vyhrazená služba HSM v Azure má dvě odlišné charakteristiky. Za prvé, registraci a nasazení v Azure zařízení HSM se svými základními síťovými součástmi. Za druhé se konfigurace zařízení HSM připravuje pro použití nebo integraci s danými úlohami nebo aplikacemi. I když se zařízení HSM Thales Luna Network v Azure shodují, protože byste si koupili přímo od Thales, je fakt, že se jedná o prostředek v Azure, vytváří několik jedinečných důležitých informací. Tyto informace a případné Doporučené postupy pro řešení potíží a osvědčené postupy jsou popsané tady, abyste zajistili vysokou viditelnost a přístup k důležitým informacím. Po použití služby jsou konečné informace k dispozici prostřednictvím žádostí o podporu přímo od společnosti Microsoft nebo Thales. 

> [!NOTE]
> Je potřeba si uvědomit, že před provedením jakékoli konfigurace na nově nasazeném zařízení HSM by se měla aktualizovat všechny relevantní opravy. Konkrétní požadovaná oprava je [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) na portálu podpory Thales, který řeší problém, při kterém systém přestane reagovat během restartování.

## <a name="hsm-registration"></a>Registrace HSM

Vyhrazený modul hardwarového zabezpečení (HSM) není volně dostupný pro použití, protože dodává hardwarové prostředky v cloudu, a proto je cenným prostředkem, který je potřeba chránit. Proto používáme proces přidávání do seznamu povolených adres prostřednictvím e-mailu HSMrequest@microsoft.com . 

### <a name="getting-access-to-dedicated-hsm"></a>Získání přístupu k vyhrazenému modulu HSM

Pokud jste přesvědčeni, že modul HARDWAROVÉho zabezpečení bude vyhovovat vašim požadavkům na úložiště klíčů, pak e-mail HSMrequest@microsoft.com s žádostí o přístup. Osnova aplikace, oblastí, které byste chtěli HSM, a objemu HSM, které hledáte. Pokud pracujete se zástupcem Microsoftu, jako je například vedoucí pro vedení účtu nebo architekt cloudového řešení, pak je zahrňte do libovolné žádosti.

## <a name="hsm-provisioning"></a>Zřizování modulu HSM

Zřízení zařízení HSM v Azure se dá udělat buď prostřednictvím rozhraní příkazového řádku nebo PowerShellu. Při registraci pro službu bude k dispozici Ukázková šablona ARM a pomoc při počátečním přizpůsobení. 

### <a name="hsm-deployment-failure-information"></a>Informace o chybě nasazení modulu HSM

Vyhrazený modul HARDWAROVÉho zabezpečení podporuje CLI a PowerShell pro nasazení, takže informace o chybách na portálu jsou omezené a nemusejí být podrobné. Lepší informace lze najít pomocí Průzkumník prostředků. Na domovské stránce portálu je ikona pro tuto položku a podrobnější informace o chybě jsou k dispozici. Tyto informace při vytváření žádosti o podporu týkající se problémů s nasazením pomáhají při vložení do dávky.

![Informace o chybě](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>Delegování podsítě HSM
Jedním z důvodů selhání nasazení je forgetting nastavit příslušné delegování pro podsíť definovanou zákazníkem, na které se zřídí HSM. Nastavení delegování je součástí virtuální sítě a požadavků podsítě pro nasazení a další podrobnosti najdete v kurzech.

![Delegování podsítě](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>Podmínka časování nasazení HSM

Šablona Standard ARM, která se poskytuje pro nasazení, má prostředky související s bránou HSM a ExpressRoute. Síťové prostředky jsou závislosti pro úspěšné nasazení HSM a časování může být klíčové.  Občas jsme viděli chyby nasazení související s problémy se závislostmi a opětovným spuštěním nasazení se často vyřeší problém. V takovém případě je odstranění prostředků a opětovné nasazení často úspěšné. Po pokusu o to a i nadále najít problém vyvolejte požadavek na podporu v Azure Portal vyberte typ problému "problémy s konfigurací instalace Azure".

### <a name="hsm-deployment-using-terraform"></a>Nasazení HSM pomocí Terraformu

Několik zákazníků používalo jako prostředí automatizace Terraformu místo šablon ARM, které jsou k dispozici při registraci této služby. HSM nejde nasadit tímto způsobem, ale závislé síťové prostředky můžou. Terraformu má modul, který by měl volat na šablonu Minimal ARM, která jut má nasazení HSM.  V této situaci by se mělo dbát na to, aby před nasazením HSM byly plně nasazené síťové prostředky, jako třeba požadovaná brána ExpressRoute. Následující příkaz rozhraní příkazového řádku je možné použít k otestování dokončeného nasazení a integrovaný podle požadavků. Nahraďte lomené závorky umístěním držitelů pro konkrétní pojmenování. Měli byste Hledat výsledek "provisioningState je úspěšný".

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Selhání nasazení na základě kvóty
Nasazení můžou selhat, pokud překročíte 2 HSM na razítko a 4 HSM na oblast. Abyste se vyhnuli této situaci, před opětovným nasazením se ujistěte, že jste odstranili prostředky z dříve neúspěšných nasazení. Chcete-li kontrolovat prostředky, přečtěte si níže uvedenou položku Návody viz HSM. Pokud se domníváte, že tuto kvótu potřebujete překročit, což je primárně v rámci ochrany, pak prosím pošlete e-mail HSMrequest@microsoft.com s podrobnostmi.

### <a name="deployment-failure-based-on-capacity"></a>Selhání nasazení na základě kapacity
Když se konkrétní razítko nebo oblast zaplní, to znamená, že jsou skoro všechny bezplatné HSM zřízené, může to vést k selháním nasazení. Každé razítko má k dispozici 11 HSM pro zákazníky, což znamená 22 na oblast. V každém razítku jsou také 3 náhr a 1 testovací zařízení. Pokud se domníváte, že jste si myslíte, že jste dosáhli limitu, pak můžete poslat HSMrequest@microsoft.com informace na úrovni výplně konkrétních razítek.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Návody se při zřizování zobrazuje HSM?
Vzhledem k vyhrazenému modulu HSM, který je na seznamu povolených služeb, se v Azure Portal považuje za "skrytý typ". Chcete-li zobrazit prostředky HSM, je nutné zaškrtnout políčko Zobrazit skryté typy, jak je znázorněno níže. Prostředek síťových adaptérů vždy sleduje modul hardwarového zabezpečení (HSM) a je dobrým místem, kde můžete zjistit IP adresu modulu HARDWAROVÉho zabezpečení před použitím SSH pro připojení.

![Snímek obrazovky, který zvýrazní kontrolu skrytých typů](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Síťové prostředky

Nasazení vyhrazeného modulu hardwarového zabezpečení (HSM) závisí na síťových prostředcích a na některých následných omezeních.

### <a name="provisioning-expressroute"></a>Zřizování ExpressRoute

Vyhrazený HSM používá ExpressRoute bránu jako tunel pro komunikaci mezi privátním adresním prostorem IP adres zákazníka a fyzickým modulem HSM v datacentru Azure.  Vzhledem k tomu, že existuje omezení jedné brány na virtuální síť, zákazníci, kteří vyžadují připojení k místním prostředkům přes ExpressRoute, budou muset pro toto připojení použít jinou virtuální síť.  

### <a name="hsm-private-ip-address"></a>Privátní IP adresa HSM

Ukázkové šablony poskytované pro vyhrazený modul HSM předpokládá, že IP modul HSM se automaticky vezme z daného rozsahu podsítě. Pomocí atributu "NetworkInterfaces" v šabloně ARM můžete zadat explicitní IP adresu pro modul hardwarového zabezpečení (HSM). 

![Snímek obrazovky, který zobrazuje ukázkovou šablonu pro vyhrazený modul HSM.](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>Inicializace HSM

Inicializace připraví nový modul HARDWAROVÉho zabezpečení pro použití nebo existující modul HARDWAROVÉho zabezpečení pro opakované použití. Inicializace modulu HSM musí být dokončena předtím, než budete moci vygenerovat nebo uložit objekty, umožníte klientům připojit se nebo provádět kryptografické operace.

### <a name="lost-credentials"></a>Ztracené přihlašovací údaje

Ztráta hesla správce prostředí bude mít za následek ztrátu materiálu klíče HSM. Pro resetování modulu HSM by měla být učiněna žádost o podporu.
Při inicializaci modulu hardwarového zabezpečení (HSM) bezpečně ukládat přihlašovací údaje. Přihlašovací údaje prostředí a HSM by se měly uchovávat v souladu se zásadami vaší společnosti.

### <a name="failed-logins"></a>Neúspěšná přihlášení

Poskytování nesprávných přihlašovacích údajů HSM může mít destruktivní důsledky. Níže jsou uvedené výchozí chování pro role HSM.

| Role | Prahová hodnota (počet pokusů) | Výsledek příliš velkého počtu chybných pokusů o přihlášení | Obnovovací |
|--|--|--|--|
| HSM | 3 |  Modul hardwarového zabezpečení (HSM) je nulový (všechny identity objektů HSM a všechny oddíly se odešlou).  |  Modul HARDWAROVÉho zabezpečení musí být znovu inicializovaný. Obsah je možné obnovit ze zálohy. | 
| Rozdělit na oddíly | 10 |  Oddíl je nulový. |  Oddíl je nutné znovu inicializovat. Obsah se může obnovit ze zálohy. |  
| Auditování | 10 | Účtů | Po 10 minutách odemčení automaticky. |  
| Kryptografický pracovník | 10 (může být sníženo) | Pokud zásada HSM 15: Povolit resetování kódu PIN oddílu na hodnotu 1 (povoleno), jsou role CO a CU zamčené.<br>Pokud zásada HSM 15: Povolit resetování kódu PIN oddílu na hodnotu 0 (zakázáno), role CO a CU se trvale zamkne a obsah oddílu už nebude přístupný. Toto je výchozí nastavení. | Role CO musí být odemčená a přihlašovací údaje, které oddíl vynulovat, pomocí `role resetpw -name co` .<br>Oddíl se musí znovu inicializovat a klíč se obnovil ze zálohovacího zařízení. |  

## <a name="hsm-configuration"></a>Konfigurace HSM 

Následující položky jsou situace, kdy jsou chyby konfigurace buď společné, nebo mají dopad na důvěryhodného volání:

### <a name="hsm-documentation-and-software"></a>Dokumentace modulu HSM a software
Software a dokumentace pro zařízení HSM Thales SafeNet Luna 7 není od Microsoftu k dispozici a je nutné ji stáhnout přímo z Thales. Registrace se vyžaduje pomocí ID zákazníka Thales přijatého během procesu registrace. Zařízení, která poskytuje Microsoft, mají software verze 7,2 a firmware verze 7.0.3. Brzy v 2020 Thales se dokumentace zveřejňuje a najdete ji [tady](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).  

### <a name="hsm-networking-configuration"></a>Konfigurace sítě HSM

Při konfiguraci sítě v rámci modulu HARDWAROVÉho zabezpečení buďte opatrní.  Modul hardwarového zabezpečení (HSM) má připojení prostřednictvím brány ExpressRoute z privátního adresního prostoru zákazníka přímo k modulu HSM.  Tento komunikační kanál je jenom pro zákaznickou komunikaci a Microsoft nemá přístup. Pokud je modul HARDWAROVÉho zabezpečení nakonfigurovaný takovým způsobem, že by to ovlivnilo tuto síťovou cestu, znamená to, že se odebere veškerá komunikace s modulem HARDWAROVÉho zabezpečení.  V této situaci jedinou možností je vyvolat žádost o podporu Microsoftu prostřednictvím Azure Portal, aby se zařízení obnovilo. Tato procedura resetování nastaví modul HARDWAROVÉho zabezpečení zpátky na jeho počáteční stav a ztratí se všechny konfigurace a materiál klíče.  Konfigurace musí být znovu vytvořená a když se zařízení připojí ke skupině HA, získá klíč k replikovanému materiálu.  

### <a name="hsm-device-reboot"></a>Restartování zařízení HSM

Některé změny konfigurace vyžadují, aby byl modul hardwarového zabezpečení (HSM) nebo restartován z elektrického počítače. Testování modulu HARDWAROVÉho zabezpečení v Azure bylo zjištěno, že v některých případech může restartování přestat reagovat. Denásobení je, že žádost o podporu musí být vytvořena v Azure Portal požadujícím pevný restart, což může trvat až 48 hodin, než se dokončí ruční zpracování v datovém centru Azure.  Abyste se vyhnuli této situaci, ujistěte se, že jste nasadili opravu restartování dostupnou přímo z Thales. Přečtěte si [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) v tématu Thales Luna Network HSM 7,2 downloads pro doporučenou opravu pro problém, při kterém systém přestane reagovat během restartování (Poznámka: budete muset být registrováni na portálu podpory Thales ke stažení).

### <a name="ntls-certificates-out-of-sync"></a>Nesynchronizované certifikáty NTLS
Klient může přijít o připojení ke HARDWAROVÉmu zabezpečení, pokud vyprší platnost certifikátu nebo byl přepsán prostřednictvím aktualizací konfigurace. Konfigurace klienta Certificate Exchange by se měla znovu použít s každým modulem HSM.
Příklad protokolování NTLS s neplatným certifikátem:

> NTLS [8508]: info: 0: příchozí žádost o připojení...: 192.168.50.2/59415 NTLS [8508]: chybová zpráva od SSLAccept je: Chyba: 14094418: rutiny SSL: ssl3_read_bytes: TLSv1 upozornění neznámá certifikační autorita NTLS [8508]: Chyba během přijetí protokolu SSL (RC_SSL_ERROR) NTLS [8508]: info: 0xc0000711: nepovedlo se vytvořit zabezpečený kanál s klientem: 192.168.50.2/59415: RC_SSL_FAILED_HANDSHAKE NTLS [8508]: informace: 0: NTLS klient "Neznámý název hostitele" instance připojení byla odebrána: 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Neúspěšná komunikace TCP

Komunikace mezi instalací klienta Luna do modulu HSM vyžaduje minimálně port TCP 1792. Vezměte v úvahu, že se v prostředí změní všechny konfigurace sítě.

### <a name="failed-ha-group-member-doesnt-recover"></a>Neúspěšný člen skupiny HA se nepovedlo obnovit.

Pokud se člen skupiny s neúspěšnými HA neobnoví, musí se ručně obnovit z klienta Luna pomocí příkazu hagroup Recovery.
Je nutné nakonfigurovat počet opakování pro skupinu HA, aby bylo možné povolit automatické obnovení. Ve výchozím nastavení se skupina HA nepokusí obnovit člena HA do skupiny při obnovení.

### <a name="ha-group-doesnt-sync"></a>Skupina HA se nesynchronizuje.

V případě, že členské oddíly nemají stejnou doménu klonování, příkaz synchronizovat v systému se zobrazí takto: Upozornění: synchronizace může selhat.  Členové ve slotu 0 a pozici 1 mají konfliktní nastavení pro klonování privátního klíče.
Do skupiny HA se přidá nový oddíl se správnou doménou klonování, po kterém následuje odebrání nesprávně nakonfigurovaného oddílu.

## <a name="hsm-deprovisioning"></a>Zrušení zřízení HSM 

Jenom v případě, že se modul hardwarového zabezpečení (HSM) dá úplně dokončit, Microsoft ho resetuje a vrátí ho do bezplatného fondu. 

### <a name="how-to-delete-an-hsm-resource"></a>Odstranění prostředku HSM

Prostředek Azure pro modul hardwarového zabezpečení (HSM) nejde odstranit, pokud není modul HARDWAROVÉho zabezpečení ve stavu "zerod".  Před pokusem o jeho odstranění jako prostředku je proto nutné odstranit všechny klíčové materiály. Nejrychlejší způsob, jak zeroize, je získat heslo správce HSM nesprávně třikrát (Poznámka: Tento odkaz odkazuje na správce HSM správce a ne na úrovni zařízení). Prostředí Luna má `hsm -factoryreset` příkaz, který se zeroizes, ale dá se spustit jenom přes konzolu na sériovém portu a zákazníci k tomu nemají přístup.

## <a name="next-steps"></a>Další kroky

Tento článek poskytuje přehled o oblastech v rámci životního cyklu nasazení HSM, které mohou mít problémy nebo vyžadují řešení potíží nebo pečlivou pozornost. Snad tento článek vám pomůže vyhnout se zbytečným zpožděním a frustrace a pokud máte relevantní doplňky nebo změny, vyvolejte žádost o podporu od Microsoftu a sdělte nám, co potřebujete. 
