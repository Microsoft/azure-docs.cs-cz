---
title: Co je Dedicated HSM? - Azure Vyhrazené HSM | Dokumenty společnosti Microsoft
description: Přehled vyhrazeného modulu hardwarového zabezpečení Azure poskytuje funkce úložiště klíčů v rámci Azure, které splňují certifikaci FIPS 140-2 Level 3
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ad3c9d702384b8a32a9d4f0c8aebe44de4bb526e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80337173"
---
# <a name="troubleshooting"></a>Řešení potíží

Služba Azure Dedicated HSM má dvě odlišné omezující oblasti. Za prvé, registrace a nasazení zařízení HSM v Azure s jejich základní síťové součásti. Za druhé, konfigurace zařízení HSM v rámci přípravy na použití/integraci s danou pracovní zátěží nebo aplikací. Přestože zařízení HSM sítě Thales Luna jsou v Azure stejná, jako u vás, kterou byste si koupili přímo od společnosti Thales, skutečnost, že jsou prostředkem v Azure, vytváří některé jedinečné aspekty. Tyto aspekty a všechny výsledné informace o řešení potíží nebo osvědčené postupy jsou zde popsány, aby byla zajištěna vysoká viditelnost a přístup k důležitým informacím. Jakmile je služba používána, konečné informace jsou k dispozici prostřednictvím žádostí o podporu přímo společnosti Microsoft nebo společnosti Thales. 

> [!NOTE]
> Je třeba poznamenat, že před provedením jakékoli konfigurace na nově nasazeném zařízení s hsm by měl být aktualizován o všechny příslušné opravy. Konkrétní požadovanou opravu je [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) v portálu podpory Thales, který řeší problém restartování.

## <a name="hsm-registration"></a>Registrace HSM

Vyhrazený modul hardwarového zabezpečení není volně k dispozici pro použití, protože dodává hardwarové prostředky v cloudu, a proto je vzácný prostředek, který potřebuje ochranu. Proto používáme whitelisting proces HSMrequest@microsoft.comprostřednictvím e-mailu pomocí . 

### <a name="getting-access-to-dedicated-hsm"></a>Získání přístupu k vyhrazenému modulu hardwarového zabezpečení

Pokud si myslíte, že vyhrazený modul hardwarového HSMrequest@microsoft.com zabezpečení bude vyhovovat vašim požadavkům na úložiště klíčů, pak e-mail požádat o přístup. Načrtněte aplikaci, oblasti, které chcete, aby byly soubory hesel a objem hledačů hesel, které hledáte. Pokud spolupracujete se zástupcem společnosti Microsoft, například s account executive nebo architektem cloudových řešení, zahrňte je do libovolné žádosti.

## <a name="hsm-provisioning"></a>Zřizování hsm

Zřizování zařízení hsm v Azure lze provést prostřednictvím rozhraní příkazového příkazu nebo PowerShellu. Při registraci pro službu bude poskytnuta ukázková šablona ARM a bude poskytnuta pomoc pro počáteční přizpůsobení. 

### <a name="hsm-deployment-failure-information"></a>Informace o selhání nasazení služby HSM

Vyhrazený modul hardwarového zabezpečení podporuje rozhraní příkazového příkazového příkazu a prostředí PowerShell pro nasazení, takže informace o chybě na základě portálu jsou omezené a nejsou podrobné. Lepší informace lze nalézt pomocí Průzkumníka prostředků. Domovská stránka portálu má pro tento tento objekt ikonu a jsou k dispozici podrobnější informace o chybách. Tyto informace pomáhají hodně, pokud je vložen a při vytváření žádosti o podporu týkající se problémů s nasazením.

![Informace o selhání](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>Delegování podsítě HSM
Důvodem číslo jedna pro selhání nasazení je zapomínání nastavit příslušné delegování pro zákazníkem definované podsítě, na které budou zřízeny hsm. Nastavení tohoto delegování je součástí předpokladů virtuální sítě a podsítě pro nasazení a další podrobnosti najdete v kurzech.

![Delegování podsítě](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>Stav závodu nasazení hsm

Standardní šablona ARM poskytovaná pro nasazení obsahuje prostředky související s bránou HSM a ExpressRoute. Síťové prostředky jsou závislost pro úspěšné nasazení modulu hesm a načasování může být rozhodující.  V některých případě jsme viděli selhání nasazení související s problémy se závislostmi a opětovné spuštění nasazení často řeší problém. Pokud ne, odstranění prostředků a opětovné nasazení je často úspěšné. Po pokusu o tento problém a stále najít problém, zvýšit žádost o podporu na webu Azure portal výběru typu problému "Problémy konfigurace nastavení Azure".

### <a name="hsm-deployment-using-terraform"></a>Nasazení hsm pomocí Terraform

Několik zákazníků použilo Terraform jako automatizační prostředí namísto šablon ARM, které byly dodány při registraci pro tuto službu. Soubory hesmene nelze nasadit tímto způsobem, ale závislé síťové prostředky mohou. Terraform má modul pro volání na minimální arm šablonu, která jut má nasazení modulu hesm.  V takovém případě je třeba dbát na to, aby síťové prostředky, jako je například požadovaná brána ExpressRoute, byly před nasazením bezpečnostních schřů plně nasazeny. Následující příkaz příkazu příkazu příkazu cli lze použít k testování dokončeného nasazení a podle potřeby integrovaný. Vyměňte držáky míst úhlového držáku pro konkrétní pojmenování. Měli byste hledat výsledek "provisioningState je úspěšné"

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Selhání nasazení na základě kvóty
Nasazení může selhat, pokud překročíte 2 hmene na razítko a 4 hmene na oblast. Chcete-li se této situaci vyhnout, ujistěte se, že jste před znovu nasazením odstranili prostředky z dříve neúspěšných nasazení. Naleznete v části "Jak lze zobrazit hmenové soubory" níže zkontrolovat zdroje. Pokud se domníváte, že je třeba překročit tuto kvótu, která HSMrequest@microsoft.com je primárně tam jako záruka, pak prosím e-mail s podrobnostmi.

### <a name="deployment-failure-based-on-capacity"></a>Selhání nasazení na základě kapacity
Pokud se určité razítko nebo oblast stává plná, to znamená, že jsou zřízeny téměř všechny volné soubory hesmazíva, může to vést k selhání nasazení. Každé razítko má pro zákazníky k dispozici 11 přístupových vozidel, což znamená 22 na region. V každém razítku jsou také 3 náhradní díly a 1 testovací zařízení. Pokud se domníváte, že jste HSMrequest@microsoft.com dosáhli limitu, pak e-mailpro informace o úrovni vyplnit konkrétní razítka.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Jak se zobrazí hsm při zřízení?
Vzhledem k vyhrazené modul hardwarového zabezpečení je služba na seznamu povolených, je považován za "skrytý typ" na portálu Azure. Chcete-li zobrazit prostředky hsm, musíte zaškrtnout políčko Zobrazit skryté typy, jak je znázorněno níže. Prostředek nic vždy následuje hsm a je vhodné místo pro zjištění IP adresy hsm ového propojení před použitím SSH pro připojení.

![Delegování podsítě](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Síťové prostředky

Nasazení vyhrazeného modulu hardwarového zabezpečení má závislost na síťových prostředcích a některá následná omezení, která je třeba znát.

### <a name="provisioning-expressroute"></a>Zřizování ExpressRoute

Vyhrazený modul hardwarového zabezpečení používá bránu ExpressRoute jako "tunelový propojení" pro komunikaci mezi privátním IP adresním prostorem zákazníka a fyzickým modulem hardwarového zabezpečení v datovém centru Azure.  Vzhledem k tomu, že existuje omezení jedné brány na virtuální síť, zákazníci, kteří vyžadují připojení k místním prostředkům prostřednictvím ExpressRoute, budou muset pro toto připojení použít jinou virtuální síť.  

### <a name="hsm-private-ip-address"></a>Privátní IP adresa hsm

Ukázkové šablony určené pro vyhrazený modul hardwarového zabezpečení předpokládají, že adresa hardwarového zabezpečení bude automaticky odebrána z daného rozsahu podsítí. Můžete zadat explicitní IP adresu pro modul zabezpečení pomocí atributu "NetworkInterfaces" v šabloně ARM. 

![Delegování podsítě](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>Inicializace hsm

Inicializace připraví nový hsm pro použití nebo existující hsm pro opakované použití. Před generováním nebo ukládáním objektů, povolením připojení klientům nebo prováděním kryptografických operací musí být inicializace objektu heslu dokončena.

### <a name="lost-credentials"></a>Ztracená pověření

Ztráta hesla správce prostředí bude mít za následek ztrátu materiálu klíče HSM. K obnovení souboru hesm by měla být podána žádost o podporu.
Při inicializaci hsm bezpečně uložte pověření. Přihlašovací údaje prostředí a hsm by měly být uchovávány v souladu se zásadami vaší společnosti.

### <a name="failed-logins"></a>Neúspěšná přihlášení

Poskytnutí nesprávných pověření pro hsm může mít destruktivní důsledky. Následují výchozí chování pro role hmenového souboru.

| Role | Prahová hodnota (počet pokusů) | Výsledek příliš mnoha chybných pokusů o přihlášení | Obnovení |
|--|--|--|--|
| HSM SO | 3 |  HSM je nulována (všechny identity objektů hsm a všechny oddíly jsou pryč)  |  HSM musí být znovu inicializován. Obsah lze obnovit ze zálohy. | 
| Oddíl SO | 10 |  Oddíl je nulován. |  Oddíl musí být znovu inicializován. Obsah lze obnovit ze zálohy. |  
| Auditování | 10 | Uzamčení | Automaticky se odemkne po 10 minutách. |  
| Kryptografický důstojník | 10 (lze snížit) | Pokud zásady hsm 15: Povolit TAK reset ování oddílu PIN je nastavena na 1 (povoleno), CO a CU role jsou uzamčeny.<br>Pokud zásady hsm 15: Povolit TAK reset ovat oddíl PIN je nastavena na 0 (zakázáno), CO a CU role jsou trvale uzamčeny a obsah oddílu již nejsou přístupné. Toto je výchozí nastavení. | Role CO musí být odemčena a pověření `role resetpw -name co`resetována oddílem SO pomocí .<br>Oddíl musí být znovu inicializován a materiál klíče obnoven ze zálohovacího zařízení. |  

## <a name="hsm-configuration"></a>Konfigurace hsm 

Následující položky jsou situace, kdy chyby konfigurace jsou buď běžné, nebo mají vliv, který je hoden volání:

### <a name="hsm-documentation-and-software"></a>Dokumentace a software HSM
Software a dokumentace pro zařízení HSM Thales SafeNet Luna 7 nejsou od společnosti Microsoft k dispozici a musí být staženy přímo od společnosti Thales. Registrace je vyžadována pomocí Zákaznického ID společnosti Thales obdrženého během procesu registrace. Zařízení, jak je stanoveno společností Microsoft, mají software verze 7.2 a firmware verze 7.0.3. Počátkem roku 2020 thales zveřejnil dokumentaci, kterou najdete [zde](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).  

### <a name="hsm-networking-configuration"></a>Konfigurace sítě hsm

Při konfiguraci sítě v rámci vzdáleného síťového propojení s ním buďte opatrní.  HSM má připojení přes ExpressRoute Gateway z privátního IP adresového prostoru zákazníka přímo do hsm.  Tento komunikační kanál je určen pouze pro komunikaci se zákazníky a společnost Microsoft nemá přístup. Pokud je hsm nakonfigurován takovým způsobem, že tato síťová cesta je ovlivněna, znamená to, že veškerá komunikace s hsm je odebrána.  V takovém případě je jedinou možností je vyvolat žádost o podporu Microsoftu prostřednictvím portálu Azure, aby se zařízení resetovalo. Tento postup obnovení nastaví hsm zpět do původního stavu a všechny konfigurace a klíč materiál je ztracen.  Konfigurace musí být znovu vytvořena a když se zařízení připojí ke skupině HA, získá replikovaný materiál klíče.  

### <a name="hsm-device-reboot"></a>Restartování zařízení HSM

Některé změny konfigurace vyžadují, aby byl soubor hsm ování nebo restartování počítače. Microsoft testování hsm v Azure zjištěno, že v některých případech restartování může zavěsit. Z toho vyplývajícím z toho je, že požadavek na podporu musí být vytvořen na portálu Azure požadující matné restartování a jeho dokončení může trvat až 48 hodin vzhledem k tomu, že se jedná o ruční proces v datovém centru Azure.  Chcete-li se této situaci vyhnout, ujistěte se, že jste nasadili opravu restartování, která je k dispozici přímo od společnosti Thales. Viz [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) v Thales Luna Network HSM 7.2 Ke stažení pro doporučenou opravu pro restart zablokuje problém (Poznámka: budete muset mít registrované v portálu podpory Thales ke stažení).

### <a name="ntls-certificates-out-of-sync"></a>Certifikáty NTLS nesynchronizované
Klient může ztratit připojení k serveru zabezpečení, pokud vyprší platnost certifikátu nebo byl přepsán prostřednictvím aktualizací konfigurace. Konfigurace klienta výměny certifikátů by měla být znovu použita s každým hsm.
Příklad protokolování NTLS s neplatným certifikátem:

> NTLS[8508]: info : 0 : Příchozí požadavek na připojení... : 192.168.50.2/59415 NTLS[8508]: Chybová zpráva od SSLAccept je : error:14094418:SSL routines:ssl3_read_bytes:tlsv1 alert unknown ca NTLS[8508]: Chyba během SSL accept ( RC_SSL_ERROR ) NTLS[8508]: info : 0xc0000711 : Nepodařilo se vytvořit zabezpečený kanál s klientem : 192.168.50.2/59415 : RC_SSL_FAILED_HANDSHAKE NTLS[8508]: info : 0 : NTLS Client "Neznámý název hostitele" Instance připojení odstraněna : 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Neúspěšná komunikace Protokolu TCP

Komunikace z instalace klienta Luna do hsm vyžaduje minimálně port TCP 1792. Zvažte to jako všechny konfigurace sítě jsou změněny v prostředí.

### <a name="failed-ha-group-member-doesnt-recover"></a>Neúspěšný člen skupiny HA se neobnoví

Pokud se neúspěšný člen skupiny HA neobnoví, musí být ručně obnoven z klienta Luna pomocí příkazu hagroup recover.
Je nutné nakonfigurovat počet opakování pro skupinu HA povolit automatické obnovení. Ve výchozím nastavení se skupina HA nepokusí obnovit člena HA do skupiny, když se obnoví.

### <a name="ha-group-doesnt-sync"></a>Skupina HA se nesynchronizuje

V případě, že členské oddíly nemají stejnou klonovací doménu, příkaz ha synchronize zobrazí následující: Upozornění: Synchronizace může selhat.  Členové v patice 0 a slot 1 mají konfliktní nastavení pro klonování soukromého klíče.
Do skupiny HA by měl být přidán nový oddíl se správnou klonovací doménou následovaný odebráním nesprávně nakonfigurovaného oddílu.

## <a name="hsm-deprovisioning"></a>Zrušení zřízení hsm 

Pouze po úplném dokončení s hsm může být zrušeno a potom Microsoft obnoví a vrátí do fondu zdarma. 

### <a name="how-to-delete-an-hsm-resource"></a>Odstranění prostředku správce hesm

Prostředek Azure pro hsmm nelze odstranit, pokud je objekt zabezpečení ve stavu "nule".  Proto musí být všechny materiály klíče odstraněny před pokusem o jeho odstranění jako prostředek. Nejrychlejší způsob, jak nulovat, je získat heslo správce HSM špatně 3 krát (poznámka: to se týká správce HSM a ne správce úrovně zařízení). Luna shell má `hsm -factoryreset` příkaz, který nuluje, ale může být spuštěn pouze prostřednictvím konzoly na sériovém portu a zákazníci k tomu nemají přístup.

## <a name="next-steps"></a>Další kroky

Tento článek poskytuje přehled o oblastech v celém životním cyklu nasazení hsm, které mohou mít problémy nebo vyžadují řešení potíží nebo pečlivé zvážení. Doufejme, že tento článek vám pomůže vyhnout se zbytečným zpožděním a frustraci, a pokud máte relevantní dodatky nebo změny, pak vznesete žádost o podporu se společností Microsoft a dejte nám vědět. 
