---
title: Postupy zabezpečení pro výrobce – služba Zřizování zařízení Azure IoT
description: Přehledy běžných postupů zabezpečení pro výrobce OEM a výrobce zařízení, kteří připravují zařízení pro registraci do služby Azure IoT Device Provisioning Service (DPS).
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 3854f353e4ea0b78c0162681e0b89d37419105d8
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529528"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Postupy zabezpečení pro výrobce zařízení Azure IoT
Vzhledem k tomu, že další výrobci vydávají zařízení IoT, je užitečné identifikovat pokyny týkající se běžných postupů. Tento článek shrnuje doporučené postupy zabezpečení, které je třeba zvážit při výrobě zařízení pro použití se službou Azure IoT Device Provisioning Service (DPS).  

> [!div class="checklist"]
> * Výběr možností ověřování zařízení
> * Instalace certifikátů na zařízeních IoT
> * Integrace důvěryhodného modulu platformy (TPM) do výrobního procesu

## <a name="selecting-device-authentication-options"></a>Výběr možností ověřování zařízení
Konečným cílem jakéhokoli bezpečnostního opatření zařízení IoT je vytvořit zabezpečené řešení IoT. Ale problémy, jako jsou hardwarová omezení, náklady a úroveň odborných znalostí zabezpečení, ovlivňují, které možnosti zvolíte. Váš přístup k zabezpečení dále ovlivní způsob připojení vašich zařízení IoT ke cloudu. I když existuje [několik prvků zabezpečení IoT](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) zvážit, klíčovým prvkem, který každý zákazník narazí, je jaký typ ověřování použít. 

Tři široce používané typy ověřování jsou certifikáty X.509, moduly důvěryhodné platformy (TPM) a symetrické klíče. Zatímco existují jiné typy ověřování, většina zákazníků, kteří vytvářejí řešení na Azure IoT použít jeden z těchto tří typů. Zbytek tohoto článku průzkumy klady a zápory pomocí každého typu ověřování.

### <a name="x509-certificate"></a>Certifikát X.509
Certifikáty X.509 jsou typ digitální identity, kterou můžete použít pro ověřování. Standard certifikátu X.509 je dokumentován v [dokumentu IETF RFC 5280](https://tools.ietf.org/html/rfc5280). V Azure IoT existují dva způsoby ověřování certifikátů:
- Miniatura. Algoritmus kryptografického otisku je spuštěn na certifikátu pro generování šestnáctkového řetězce. Generovaný řetězec je jedinečný identifer pro certifikát. 
- Ověřování certifikační autority na základě celého řetězce. Řetěz certifikátů je hierarchický seznam všech certifikátů potřebných k ověření certifikátu koncové entity (EE). Chcete-li ověřit certifikát EE, je nutné ověřit každý certifikát v řetězci, včetně důvěryhodného kořenového certifikačního autority. 

Profesionálové pro X.509:
- X.509 je nejbezpečnější typ ověřování podporovaný v Azure IoT.
- X.509 umožňuje vysokou úroveň kontroly pro účely správy certifikátů.
- Mnoho dodavatelů jsou k dispozici poskytovat řešení ověřování založené na X.509.

Nevýhody pro X.509:
- Mnoho zákazníků může potřebovat spoléhat na externí dodavatele pro jejich certifikáty.
- Správa certifikátů může být nákladná a zvyšuje celkové náklady na řešení.
- Správa životního cyklu certifikátů může být obtížná, pokud logistika není dobře promyšlená. 

### <a name="trusted-platform-module-tpm"></a>Čip TPM (Trusted Platform Module)
Čip TPM, známý také jako [ISO/IEC 11889](https://www.iso.org/standard/66510.html), je standardpro bezpečné generování a ukládání kryptografických klíčů. Čip TPM také odkazuje na virtuální nebo fyzické vstupně-o zařízení, které spolupracuje s moduly, které implementují standard. Zařízení TPM může existovat jako samostatný hardware, integrovaný hardware, modul založený na firmwaru nebo softwarový modul. 

Mezi tpms a symetrickými klávesami existují dva klíčové rozdíly: 
- Čipy TPM mohou také ukládat certifikáty X.509.
- Ověření čipu TPM v systému DPS používá ověřovací klíč ČiptPM (EK), což je forma asymetrického ověřování. Při asymetrickém ověřování se pro šifrování používá veřejný klíč a pro dešifrování se používá samostatný soukromý klíč. Naproti tomu symetrické klíče používají symetrické ověřování, kde se soukromý klíč používá pro šifrování i dešifrování. 

Klady pro čip TPM:
- TPM jsou součástí standardního hardwaru na mnoha zařízeních se systémem Windows s integrovanou podporou operačního systému. 
- Ověření čipu TPM je snadněji zabezpečeno než ověření symetrického klíče založené na sdíleném přístupovém podpisu (SAS).
- Můžete snadno vyprší platnost a obnovit, nebo roll, pověření zařízení. DPS automaticky shrnuje přihlašovací údaje centra IoT Hub vždy, když je zařízení TPM splatné pro opětovné zřízení.

Nevýhody pro čip TPM: 
- TPM jsou složité a může být obtížné používat. 
- Vývoj aplikací s tpms je obtížné, pokud máte fyzický Čip TPM nebo emulátor kvality.
- Možná budete muset přepracovat desku zařízení tak, aby do hardwaru zahrnula čip TPM. 
- Pokud srolujete EK na čipu TPM, zničí identitu čipu TPM a vytvoří novou. I když fyzický čip zůstane stejný, má novou identitu ve vašem řešení IoT.

### <a name="symmetric-key"></a>Symetrický klíč
U symetrických klíčů se stejný klíč používá k šifrování a dešifrování zpráv. V důsledku toho je stejný klíč znám jak pro zařízení, tak pro službu, která jej ověřuje. Azure IoT podporuje symetrické klíče založené na tokenech SAS. Symetrické ověřování pomocí klíče vyžaduje významnou odpovědnost vlastníka za zabezpečení klíčů a dosažení stejné úrovně zabezpečení s ověřováním X.509. Pokud používáte symetrické klíče, doporučujeme chránit klíče pomocí modulu hardwarového zabezpečení (HSM).

Klady pro symetrický klíč:
- Použití symetrických klíčů je nejjednodušší a nejlevnější způsob, jak začít s ověřováním.
- Použití symetrických klíčů zjednodušuje proces, protože není nic navíc generovat. 

Nevýhody pro symetrický klíč: 
- Symetrické klávesy vynaloží značné úsilí na zabezpečení klíčů. Stejný klíč je sdílen mezi zařízením a cloudem, což znamená, že klíč musí být chráněn na dvou místech. Naproti tomu výzva s certifikáty TPM a X.509 dokazuje vlastnictví veřejného klíče bez odhalení soukromého klíče.
- Symetrické klíče usnadňují sledování špatných postupů zabezpečení. Běžnou tendencí se symetrickými klíči je pevný kód nešifrovaných klíčů na zařízeních. I když je tato praxe pohodlná, ponechává klíče zranitelné. Určitá rizika můžete zmírnit bezpečným uložením symetrického klíče na zařízení. Pokud je však vaší prioritou nakonec zabezpečení, nikoli pohodlí, použijte k ověření certifikáty X.509 nebo Čip TPM. 

### <a name="shared-symmetric-key"></a>Sdílený symetrický klíč
Existuje varianta symetrického ověřování klíčů označovaná jako sdílený symetrický klíč. Tento přístup zahrnuje použití stejného symetrického klíče ve všech zařízeních. Doporučujeme vyhnout se použití sdílených symetrických klíčů na vašich zařízeních. 

Pro pro sdílený symetrický klíč:
- Jednoduché na implementaci a levné na výrobu ve velkém měřítku. 

Nevýhody pro sdílený symetrický klíč: 
- Vysoce zranitelní vůči útoku. Přínos snadné implementace je daleko převážen rizikem. 
- Kdokoli může zosobnit vaše zařízení, pokud získá sdílený klíč.
- Pokud se spoléháte na sdílený symetrický klíč, který se stane ohrožen, pravděpodobně ztratíte kontrolu nad zařízeními. 

### <a name="making-the-right-choice-for-your-devices"></a>Správná volba pro vaše zařízení
Chcete-li zvolit metodu ověřování, ujistěte se, že vezmete v úvahu výhody a náklady každého přístupu pro jedinečný výrobní proces.  Pro ověřování zařízení obvykle existuje inverzní vztah mezi tím, jak bezpečný je daný přístup a jak je pohodlný.  

## <a name="installing-certificates-on-iot-devices"></a>Instalace certifikátů na zařízeních IoT
Pokud k ověření zařízení IoT používáte certifikáty X.509, tato část nabízí pokyny, jak integrovat certifikáty do výrobního procesu. Budete muset udělat několik rozhodnutí.  Patří mezi ně rozhodnutí o běžných proměnných certifikátů, při generování certifikátů a při jejich instalaci. 

Pokud jste zvyklí používat hesla, můžete se zeptat, proč nemůžete používat stejný certifikát ve všech svých zařízeních, stejně jako byste mohli používat stejné heslo ve všech svých zařízeních. Za prvé, použití stejného hesla všude je nebezpečné. Tato praxe vystavila společnosti velkým DDoS útokům, včetně toho, který před několika lety sestřelil DNS na východním pobřeží USA. Nikdy nepoužívejte stejné heslo všude, a to i s osobními účty. Za druhé, certifikát není heslo, je to jedinečná identita. Heslo je jako tajný kód, který může kdokoli použít k otevření dveří v zabezpečené budově.  Je to něco, co víte, a můžete dát heslo každému, kdo získá vstup.  Certifikát je jako řidičský průkaz s fotografií a dalšími detaily, které můžete ukázat strážnému, aby se dostal do zabezpečené budovy. Je to spojené s tím, kdo jsi.  Za předpokladu, že strážný přesně odpovídá lidem s řidičskými průkazy, pouze vy můžete použít svůj řidičský průkaz (identitu) k získání vstupu. 

### <a name="variables-involved-in-certificate-decisions"></a>Proměnné zapojené do rozhodování o certifikátech
Zvažte následující proměnné a jak každá z nich ovlivňuje celkový výrobní proces. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Odkud pochází kořen důvěryhodnosti certifikátu
Správa infrastruktury veřejných klíčů (PKI) může být nákladná a složitá.  Zvláště pokud vaše společnost nemá žádné zkušenosti se správou PKI. Máte tyto možnosti:
- Použijte pki od jiného výrobce. Zprostředkující podpisové certifikáty můžete zakoupit od jiného dodavatele certifikátů. Nebo můžete použít soukromou certifikační autoritu (CA). 
- Použijte samoobslužnou pki. Můžete udržovat svůj vlastní systém PKI a vytvářet vlastní certifikáty.
- Použijte službu zabezpečení [Azure Sphere.](https://azure.microsoft.com/services/azure-sphere/) Tato možnost platí jenom pro zařízení Azure Sphere. 

#### <a name="where-certificates-are-stored"></a>Kde jsou certifikáty uloženy
Existuje několik faktorů, které ovlivňují rozhodnutí o tom, kde jsou uloženy certifikáty. Mezi tyto faktory patří typ zařízení, očekávané ziskové marže (zda si můžete dovolit zabezpečené úložiště), možnosti zařízení a stávající technologie zabezpečení na zařízení, které můžete používat. Zvažte následující možnosti:
- V modulu hardwarového zabezpečení (HSM). Použití hsm je vysoce doporučeno. Zkontrolujte, zda ovládací deska vašeho zařízení již nemá nainstalovaný soubor HSM. Pokud víte, že nemáte modul hardwarového a hardwarového modulu, spolupracujte s výrobcem hardwaru a určete modul hardwarového modulu, který vyhovuje vašim potřebám.
- Na bezpečném místě na disku, jako je například důvěryhodné prostředí pro spuštění (TEE).
- V místním systému souborů nebo v úložišti certifikátů. Například úložiště certifikátů systému Windows. 

#### <a name="connectivity-at-the-factory"></a>Konektivita ve výrobě
Připojení při výrobě určuje, jak a kdy získáte certifikáty k instalaci na zařízení. Možnosti připojení jsou následující:
- Připojení. Připojení je optimální, zjednodušuje proces generování certifikátů místně. 
- Žádné připojení. V takovém případě použijete podepsaný certifikát certifikační autority ke generování certifikátů zařízení místně i offline. 
- Žádné připojení. V takovém případě můžete získat certifikáty, které byly vygenerovány předem. Nebo můžete použít offline pki ke generování certifikátů místně.

#### <a name="audit-requirement"></a>Požadavek na audit
V závislosti na typu zařízení, která vyrábíte, můžete mít regulační požadavek na vytvoření auditní stopy o tom, jak jsou identity zařízení nainstalovány na vašich zařízeních. Auditování zvyšuje značné výrobní náklady. Takže ve většině případů to udělejte pouze v případě potřeby. Pokud si nejste jisti, zda je audit vyžadován, obraťte se na právní oddělení vaší společnosti. Možnosti auditování jsou: 
- Není to citlivé odvětví. Auditování není vyžadováno.
- Citlivý průmysl. Certifikáty by měly být instalovány v zabezpečené místnosti v souladu s požadavky na certifikaci shody. Pokud potřebujete zabezpečenou místnost pro instalaci certifikátů, pravděpodobně již víte, jak se certifikáty instalují do vašich zařízení. A pravděpodobně již máte systém auditu na místě. 

#### <a name="length-of-certificate-validity"></a>Délka platnosti osvědčení
Podobně jako u řidičského průkazu mají certifikáty datum vypršení platnosti, které je nastaveno při jejich vytvoření. Zde jsou možnosti délky platnosti certifikátu:
- Obnovení není nutné.  Tento přístup používá dlouhé období obnovení, takže nikdy nebudete muset obnovovat certifikát během životnosti zařízení. I když je takový přístup pohodlný, je také riskantní.  Riziko můžete snížit pomocí zabezpečeného úložiště, jako je bezpečnostní počítač na vašich zařízeních. Doporučuje se však vyhnout se používání dlouhodobých certifikátů.
- Je nutné obnovení.  Certifikát budete muset obnovit během životnosti zařízení. Délka platnosti certifikátu závisí na kontextu a budete potřebovat strategii pro obnovení.  Strategie by měla zahrnovat, kde získáváte certifikáty a jaký typ funkcí pro vysílání musí vaše zařízení používat v procesu obnovení. 

### <a name="when-to-generate-certificates"></a>Kdy generovat certifikáty
Možnosti připojení k internetu ve vaší továrně ovlivní váš proces generování certifikátů. Máte několik možností, kdy generovat certifikáty: 

- Předinstalované certifikáty.  Někteří dodavatelé hme nabízejí prémiovou službu, ve které dodavatel hsm nainstaluje certifikáty pro zákazníka. Za prvé, zákazníci udělit dodavateli hsm přístup k podpisového certifikátu. Dodavatel hsm nainstaluje certifikáty podepsané tímto podpisovým certifikátem do každého hsm, který zákazník koupí. Vše, co zákazník musí udělat, je nainstalovat hsm na zařízení. I když tato služba zvyšuje náklady, pomáhá zefektivnit váš výrobní proces.  A řeší otázku, kdy nainstalovat certifikáty.
- Certifikáty generované zařízením.  Pokud vaše zařízení generují certifikáty interně, musíte extrahovat veřejný certifikát X.509 ze zařízení, abyste ho zaregistrovali do DPS. 
- Připojená továrna.  Pokud má vaše továrna připojení, můžete kdykoli generovat certifikáty zařízení.
- Offline továrna s vlastní pki. Pokud vaše továrna nemá připojení a používáte vlastní podporu pki s podporou offline, můžete certifikáty vygenerovat, když je potřebujete.
- Offline továrna s pki od jiných výrobců. Pokud vaše továrna nemá připojení a používáte pki jiného výrobce, musíte certifikáty vygenerovat předem. A bude nutné generovat certifikáty z umístění, které má připojení. 

### <a name="when-to-install-certificates"></a>Kdy nainstalovat certifikáty
Po generování certifikátů pro vaše zařízení IoT, můžete je nainstalovat do zařízení. 

Pokud používáte předinstalované certifikáty s hsm, proces je zjednodušen. Po instalaci hsm v zařízení, kód zařízení k němu přístup. Pak budete volat hsm API pro přístup k certifikátu, který je uložen v hsm. Tento přístup je nejvhodnější pro váš výrobní proces. 

Pokud nepoužíváte předinstalovaný certifikát, musíte jej nainstalovat jako součást výrobního procesu. Nejjednodušším přístupem je instalace certifikátu do hsm současně s blikáním počátečního obrazu firmwaru. Proces musí přidat krok k instalaci bitové kopie na každé zařízení. Po tomto kroku můžete před odesláním a odesláním zařízení spustit závěrečné kontroly kvality a další kroky. 

K dispozici jsou softwarové nástroje, které vám umožní spustit proces instalace a konečnou kontrolu kvality v jednom kroku. Tyto nástroje můžete upravit tak, aby generovaly certifikát nebo vytahovaly certifikát z předem generovaného úložiště certifikátů. Pak software může nainstalovat certifikát tam, kde je třeba jej nainstalovat. Softwarové nástroje tohoto typu umožňují provozovat výrobu kvality výroby ve velkém měřítku. 

Po instalaci certifikátů na zařízeních je dalším krokem naučit se zaregistrovat zařízení pomocí [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integrace čipu TPM do výrobního procesu
Pokud k ověření zařízení IoT používáte čip TPM, tato část nabízí pokyny. Pokyny se týkají široce používaných zařízení TPM 2.0, která mají podporu klíče na základě hash (HMAC). Specifikace čipu TPM pro čipy TPM je standard ISO, který spravuje skupina Trusted Computing Group. Další informace o čipu TPM naleznete ve specifikacích pro [čipy TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) a [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Převzetí vlastnictví čipu TPM
Důležitým krokem při výrobě zařízení s čipem TPM je převzetí vlastnictví čipu TPM. Tento krok je povinný, abyste mohli zadat klíč pro vlastníka zařízení. Prvním krokem je extrahovat ověřovací klíč (EK) ze zařízení. Dalším krokem je skutečně nárok ovat. Způsob dosažení tohoto cíle závisí na tom, který čip TPM a operační systém používáte. V případě potřeby se obraťte na výrobce čipu TPM nebo vývojáře operačního systému zařízení a zjistěte, jak požadovat vlastnictví. 

Ve vašem výrobním procesu můžete extrahovat EK a nárokovat vlastnictví v různých časech, což zvyšuje flexibilitu. Mnoho výrobců využívá této flexibility přidáním modulu hardwarového zabezpečení (HSM) pro zvýšení zabezpečení svých zařízení. Tato část obsahuje pokyny, kdy extrahovat EK, kdy nárokovat vlastnictví čipu TPM, a důležité informace pro integraci těchto kroků do výrobní časové osy. 

> [!IMPORTANT]
> Následující pokyny předpokládají, že používáte diskrétní, firmware nebo integrovaný čip TPM. V místech, kde je to použitelné, pokyny přidá poznámky k použití nediskrétní nebo software TPM. Pokud používáte software TPM, může být další kroky, které tyto pokyny neobsahuje. Softwarové tpms mají řadu implementací, které jsou nad rámec tohoto článku.  Obecně je možné integrovat software TPM do následující obecné výrobní časové osy. Software emulovaný čip TPM je sice vhodný pro vytváření prototypů a testování, ale nemůže poskytovat stejnou úroveň zabezpečení jako diskrétní, firmware nebo integrovaný čip TPM. Obecně se vyhněte používání softwarového čipu TPM ve výrobě.

### <a name="general-manufacturing-timeline"></a>Obecná výrobní časová osa
Následující časová osa ukazuje, jak čip TPM prochází výrobním procesem a končí v zařízení. Každý výrobní proces je jedinečný a tato časová osa zobrazuje nejběžnější vzory. Časová osa nabízí pokyny, kdy provést určité akce s klíči. 

#### <a name="step-1-tpm-is-manufactured"></a>Krok 1: Čip TPM se vyrábí
- Pokud si koupíte tpmod od výrobce pro použití ve vašich zařízeních, podívejte se, jestli budou extrahovat veřejné klíče pro potvrzení (EK_pubs) pro vás. Je užitečné, pokud výrobce poskytne seznam EK_pubs s dodanými zařízeními. 
    > [!NOTE]
    > Výrobce čipu TPM může udělit přístup k zápisu do seznamu registrací pomocí zásad sdíleného přístupu ve službě zřizování.  Tento přístup jim umožňuje přidat TPM do seznamu zápisů za vás.  Ale to je v rané fázi výrobního procesu a vyžaduje důvěru ve výrobce Čipu TPM. Udělejte to na vlastní nebezpečí. 

- Pokud vyrábíte tpms prodávat výrobcům zařízení, zvažte poskytnutí seznamu EK_pubs svým zákazníkům spolu s jejich fyzické TPM.  Poskytování EK_pubs zákazníkům ušetří krok v jejich procesu. 
- Pokud vyrábíte tpms pro použití s vlastními zařízeními, určit, který bod v procesu je nejvhodnější extrahovat EK_pub. EK_pub můžete extrahovat v libovolném zbývajícím bodě časové osy. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Krok 2: Čip TPM je nainstalován do zařízení
V tomto okamžiku ve výrobním procesu byste měli vědět, s jakou instancí DPS bude zařízení použito. V důsledku toho můžete přidat zařízení do seznamu registrací pro automatické zřizování. Další informace o automatickém zřizování zařízení naleznete v [dokumentaci K sDPS](about-iot-dps.md).
- Pokud jste nezískali EK_pub, nyní je vhodná doba, aby tak učinily. 
- V závislosti na procesu instalace čipu TPM je tento krok také vhodná doba k převzetí vlastnictví čipu TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Krok 3: Zařízení má nainstalovaný firmware a software
V tomto okamžiku procesu nainstalujte klienta DPS spolu s rozsahem ID a globální adresou URL pro zřizování.
- Nyní je poslední šance získat EK_pub. Pokud do vašeho zařízení nainstaluje software třetí strana, je vhodné nejprve extrahovat EK_pub.
- Tento bod ve výrobním procesu je ideální pro převzetí vlastnictví čipu TPM.  
    > [!NOTE]
    > Pokud používáte software TPM, můžete jej nainstalovat nyní.  Extrahujte EK_pub současně.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Krok 4: Zařízení je zabaleno a odesláno do skladu
Zařízení může před nasazením sedět ve skladu po dobu 6-12 měsíců. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Krok 5: Zařízení je nainstalováno do umístění
Poté, co zařízení dorazí do konečného umístění, prochází automatizovaným zřizováním pomocí DPS.

Další informace naleznete [v tématu Autoprovisioning concepts](concepts-auto-provisioning.md) and [TPM ateststation](concepts-tpm-attestation.md). 

## <a name="resources"></a>Prostředky

Kromě doporučených postupů zabezpečení v tomto článku poskytuje Azure IoT prostředky, které vám pomohou při výběru zabezpečeného hardwaru a vytváření zabezpečených nasazení IoT: 
- [Doporučení zabezpečení](../iot-fundamentals/security-recommendations.md) Azure IoT pro řízení procesu nasazení. 
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) nabízí službu, která vám pomůže vytvořit zabezpečené nasazení IoT. 
- Nápovědu k vyhodnocení hardwarového prostředí najdete v dokumentu White Paper [Evaluating your IoT Security](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Nápovědu k výběru zabezpečeného hardwaru najdete [v tématu Správný zabezpečený hardware pro nasazení IoT](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 