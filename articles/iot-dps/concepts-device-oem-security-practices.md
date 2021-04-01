---
title: Postupy zabezpečení pro výrobce – služba Azure IoT Device Provisioning
description: V tomto článku najdete běžné postupy zabezpečení pro výrobce OEM a zařízení, kteří připravují zařízení k registraci ve službě Azure IoT Device Provisioning (DPS).
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 788738082cbf9995fb2f7282bc3f574903275528
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90527203"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Postupy zabezpečení pro výrobce zařízení Azure IoT
V případě více výrobců zařízení IoT je užitečné zjistit pokyny k běžným postupům. Tento článek shrnuje doporučené postupy zabezpečení, které je potřeba vzít v úvahu při výrobě zařízení pro použití se službou Azure IoT Device Provisioning (DPS).  

> [!div class="checklist"]
> * Výběr možností ověřování zařízení
> * Instalace certifikátů na zařízeních IoT
> * Integrace čipu TPM (Trusted Platform Module) do výrobního procesu

## <a name="selecting-device-authentication-options"></a>Výběr možností ověřování zařízení
Hlavním cílem jakékoli míry zabezpečení zařízení IoT je vytvoření zabezpečeného řešení IoT. Ale problémy, jako jsou hardwarová omezení, náklady a úroveň zabezpečení, mají vliv na to, jaké možnosti si zvolíte. Dále váš přístup k zabezpečení ovlivňuje způsob připojení zařízení IoT ke cloudu. I když existuje [několik prvků zabezpečení IoT](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) , které je potřeba vzít v úvahu, klíčovým prvkem, který každý zákazník narazí, je typ ověřování, který se má použít. 

Tři široce používané typy ověřování jsou certifikáty X. 509, TPM (Trusted Platform Module) a symetrický klíč. I když existují jiné typy ověřování, většina zákazníků, kteří sestavují řešení v Azure IoT, používá jeden z těchto tří typů. Zbytek tohoto článku proprůzkumuje odborníky a nevýhody používání jednotlivých typů ověřování.

### <a name="x509-certificate"></a>Certifikát X. 509
Certifikáty X. 509 jsou typu digitální identity, který můžete použít k ověřování. Standard certifikátu X. 509 je popsán v [dokumentu IETF RFC 5280](https://tools.ietf.org/html/rfc5280). V Azure IoT existují dva způsoby, jak ověřit certifikáty:
- Kryptografický. Algoritmus kryptografického otisku se spustí na certifikátu pro vygenerování hexadecimálního řetězce. Vygenerovaný řetězec je jedinečný identifikátorem pro certifikát. 
- Ověřování CA na základě úplného řetězu. Řetěz certifikátů je hierarchický seznam všech certifikátů potřebných k ověření certifikátu koncové entity (EE). Pokud chcete ověřit certifikát EE, je nutné ověřit každý certifikát v řetězu včetně důvěryhodné kořenové certifikační autority. 

Specialisty pro X. 509:
- X. 509 je nejbezpečnější typ ověřování podporovaný ve službě Azure IoT.
- X. 509 umožňuje vysokou úroveň řízení pro účely správy certifikátů.
- K dispozici je mnoho dodavatelů pro zajištění řešení ověřování založeného na X. 509.

Nevýhody pro X. 509:
- Mnoho zákazníků může potřebovat spoléhat na externí dodavatele pro své certifikáty.
- Správa certifikátů může být nákladná a přidává celkové náklady na řešení.
- Správa životního cyklu certifikátu může být obtížná, pokud není logistika dobře promyšlená. 

### <a name="trusted-platform-module-tpm"></a>Čip TPM (Trusted Platform Module)
ČIP TPM, označovaný také jako [ISO/IEC 11889](https://www.iso.org/standard/66510.html), představuje standard pro bezpečné generování a ukládání kryptografických klíčů. ČIP TPM také odkazuje na virtuální nebo fyzické vstupně-výstupní zařízení, které komunikuje s moduly, které implementují Standard. Zařízení TPM může existovat jako diskrétní hardware, integrovaný hardware, modul založený na firmwaru nebo softwarový modul. 

Existují dva klíčové rozdíly mezi čipy TPM a symetrickými klíči: 
- Čipy TPM můžou také ukládat certifikáty X. 509.
- Ověření identity pomocí čipu TPM v DPS používá ověřovací klíč TPM (EK), což je forma asymetrického ověřování. Při použití asymetrického ověřování se k šifrování používá veřejný klíč a k dešifrování se používá samostatný privátní klíč. Naopak symetrické klíče používají symetrické ověřování, kdy se privátní klíč používá pro šifrování i dešifrování. 

Specialisté na čip TPM:
- Čipy TPM jsou součástí řady zařízení s Windows jako standardní hardware s integrovanou podporou pro operační systém. 
- Ověření identity pomocí čipu TPM je snazší než ověřování symetrického klíče založeného na tokenech sdíleného přístupového podpisu (SAS).
- Můžete snadno vypršet platnost a prodloužit platnost přihlašovacích údajů zařízení. DPS automaticky zahrne přihlašovací údaje IoT Hub vždy, když je zařízení TPM v důsledku opětovného zřízení způsobené.

Nevýhody pro čip TPM: 
- Čipy TPM jsou složité a můžou být obtížné použít. 
- Vývoj aplikací pomocí čipy TPM je obtížné, pokud nemáte fyzický čip TPM nebo emulátor kvality.
- Možná budete muset změnit návrh panelu zařízení tak, aby zahrnoval čip TPM v hardwaru. 
- Pokud vrátíte EK do čipu TPM, zničí identitu čipu TPM a vytvoří nový. I když fyzický čip zůstává stejný, má ve vašem řešení IoT novou identitu.

### <a name="symmetric-key"></a>Symetrický klíč
Pomocí symetrických klíčů je stejný klíč použit k šifrování a dešifrování zpráv. V důsledku toho je stejný klíč známý jak pro zařízení, tak pro službu, která ho ověřuje. Azure IoT podporuje připojení symetrického klíče založeného na tokenech SAS. Ověřování pomocí symetrického klíče vyžaduje významnou zodpovědnost majitele za účelem zabezpečení klíčů a zajištění stejné úrovně zabezpečení s ověřováním X. 509. Pokud používáte symetrické klíče, doporučuje se chránit klíče pomocí modulu hardwarového zabezpečení (HSM).

Profesionály pro symetrický klíč:
- Použití symetrických klíčů je nejjednodušším a nejnižším nákladovým způsobem, jak začít s ověřováním.
- Použití symetrických klíčů zjednodušuje proces, protože není nic dalšího k vygenerování. 

Nevýhody pro symetrický klíč: 
- Symetrické klíče využívají k zabezpečení klíčů značnou úroveň. Stejný klíč se sdílí mezi zařízením a cloudem, což znamená, že klíč musí být chráněný na dvou místech. Naproti tomu se výzva s certifikáty TPM a X. 509 odhalují vlastnictví veřejného klíče bez odhalení privátního klíče.
- Symetrický klíč usnadňuje sledování slabých postupů zabezpečení. Běžným podklíči s symetrickými klíči je silné kódování nešifrovaných klíčů v zařízeních. I když je tento postup výhodný, ponechá klíče zranitelné. Můžete zmírnit riziko tím, že na zařízení bezpečně uložíte symetrický klíč. Pokud ale vaše priorita je nakonec spíše než pohodlí, použijte k ověřování certifikáty X. 509 nebo TPM. 

### <a name="shared-symmetric-key"></a>Sdílený symetrický klíč
Existuje variace ověřování symetrického klíče označovaného jako sdílený symetrický klíč. Tento přístup vyžaduje použití stejného symetrického klíče na všech zařízeních. Doporučujeme vyhnout se použití sdílených symetrických klíčů na vašich zařízeních. 

Pro pro sdílený symetrický klíč:
- Jednoduchá implementace a nenákladná výroba ve velkém měřítku. 

Nevýhody sdíleného symetrického klíče: 
- Vysoce zranitelnější vůči útokům. Výhodou jednoduché implementace je mnohem převáženo rizikem. 
- Kdokoli může zosobnit vaše zařízení, pokud získá sdílený klíč.
- Pokud se spoléháte na sdílený symetrický klíč, který je napadený, pravděpodobně ztratíte kontrolu nad zařízeními. 

### <a name="making-the-right-choice-for-your-devices"></a>Nastavení správné možnosti pro vaše zařízení
Pokud chcete zvolit metodu ověřování, nezapomeňte vzít v úvahu výhody a náklady na každý přístup k vašemu jedinečnému výrobnímu procesu.  Pro ověřování zařízení je obvykle k dispozici vztah mezi tím, jak zabezpečený daný přístup je a jak pohodlné je.  

## <a name="installing-certificates-on-iot-devices"></a>Instalace certifikátů na zařízeních IoT
Pokud k ověření zařízení IoT používáte certifikáty X. 509, Tato část nabízí pokyny k integraci certifikátů do výrobního procesu. Budete muset udělat několik rozhodnutí.  Mezi ně patří rozhodnutí o běžných proměnných certifikátu, čas generování certifikátů a jejich instalaci. 

Pokud používáte hesla, můžete se zeptat, proč nemůžete použít stejný certifikát na všech svých zařízeních, a to stejným způsobem, jakým byste mohli stejné heslo použít na všech svých zařízeních. Nejdřív používejte stejné heslo všude, kde je nebezpečné. Tento postup vykázal společnosti k významným útokům DDoS, včetně toho, který trvala služba DNS na USA – východ pobřeží před několika lety. Nikdy nepoužívejte stejné heslo všude, dokonce i s osobními účty. Za druhé, certifikát není heslo, jedná se o jedinečnou identitu. Heslo je jako tajný kód, který může kdokoli použít k otevření dvířek v zabezpečeném sestavení.  Je to něco, co znáte, a vy můžete heslo dát komukoli, abyste získali svůj úvodní text.  Certifikát je jako licence ovladače s vaší fotografií a dalšími podrobnostmi, které se dají zobrazit jako ochrana a získat tak zabezpečenou budova. Je svázána s vámi.  Za předpokladu, že ochrana přesně odpovídá osobám s licencemi na ovladače, můžete k získání úvodníku použít jenom svoji licenci (identitu). 

### <a name="variables-involved-in-certificate-decisions"></a>Proměnné zahrnuté v rozhodnutích o certifikátech
Vezměte v úvahu následující proměnné a to, jak každý z nich ovlivňuje celý výrobní proces. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Odkud pochází kořen certifikátu vztahu důvěryhodnosti
Správa infrastruktury veřejných klíčů (PKI) může být nákladná a složitá.  Obzvláště pokud vaše společnost nemá žádné zkušenosti se správou infrastruktury veřejných klíčů. Máte tyto možnosti:
- Použijte infrastrukturu veřejných klíčů třetí strany. Zprostředkující podpisové certifikáty můžete koupit od dodavatele certifikátu třetí strany. Nebo můžete použít soukromou certifikační autoritu (CA). 
- Použijte samoobslužně spravovaná infrastruktura veřejných klíčů (PKI). Můžete udržovat vlastní systém PKI a vygenerovat vlastní certifikáty.
- Použijte službu [Azure sphere](https://azure.microsoft.com/services/azure-sphere/) Security. Tato možnost se vztahuje jenom na zařízení Azure Sphere. 

#### <a name="where-certificates-are-stored"></a>Kde jsou uloženy certifikáty
Existuje několik faktorů, které mají vliv na rozhodnutí o uložení certifikátů. Mezi tyto faktory patří typ zařízení, očekávané ziskové marže (zda můžete zajistit zabezpečené úložiště), funkce zařízení a stávající technologii zabezpečení na zařízení, které budete moci použít. Vezměte v úvahu následující možnosti:
- V modulu hardwarového zabezpečení (HSM). Použití modulu hardwarového zabezpečení (HSM) se důrazně doporučuje. Ověřte, zda je v řídicím panelu zařízení již nainstalován modul HSM. Pokud víte, že nemáte modul hardwarového zabezpečení (HSM), spolupracujte se svým výrobcem hardwaru a Identifikujte modul HARDWAROVÉho zabezpečení, který vyhovuje vašim potřebám.
- Na bezpečném místě na disku, jako je například důvěryhodné spuštění prostředí (TEE).
- V místním systému souborů nebo v úložišti certifikátů. Například úložiště certifikátů systému Windows. 

#### <a name="connectivity-at-the-factory"></a>Připojení v továrně
Připojení v továrně určuje, jak a kdy získáte certifikáty, které se mají na zařízení nainstalovat. Možnosti připojení jsou následující:
- Komunikační. Když je připojení optimální, zjednodušuje proces generování certifikátů v místním prostředí. 
- Žádné připojení. V takovém případě použijete podepsaný certifikát z certifikační autority k vygenerování certifikátů zařízení místně a offline. 
- Žádné připojení. V takovém případě můžete získat certifikáty, které byly vygenerovány předem. Případně můžete použít offline infrastrukturu veřejných klíčů k vygenerování certifikátů místně.

#### <a name="audit-requirement"></a>Požadavek na audit
V závislosti na typu zařízení, která vytváříte, je možné, že budete mít regulativní požadavek na vytvoření kontrolního záznamu způsobu instalace identit zařízení do zařízení. Auditování přináší významné výrobní náklady. Takže ve většině případů to stačí pouze v případě potřeby. Pokud si nejste jistí, jestli je audit nutný, poraďte se s právním oddělením vaší společnosti. Možnosti auditování: 
- Nejedná se o citlivé odvětví. Není vyžadováno žádné auditování.
- Citlivé obory. Certifikáty by se měly instalovat v zabezpečené místnosti podle požadavků certifikace na dodržování předpisů. Pokud potřebujete zabezpečenou místnost pro instalaci certifikátů, pravděpodobně už víte, jak se certifikáty nainstalují do vašich zařízení. A pravděpodobně již máte místní systém auditu. 

#### <a name="length-of-certificate-validity"></a>Délka platnosti certifikátu
Podobně jako u licence ovladače mají certifikáty datum vypršení platnosti, které je nastaveno při jejich vytvoření. Tady jsou možnosti pro délku platnosti certifikátu:
- Obnovení není vyžadováno.  Tento přístup využívá dlouhou dobu obnovení, takže během životnosti zařízení nikdy nemusíte certifikát obnovovat. I když je takový přístup pohodlný, je také riskantní.  Riziko můžete snížit použitím zabezpečeného úložiště, jako je modul HARDWAROVÉho zabezpečení, na svých zařízeních. Doporučeným postupem je ale vyhnout se použití dlouhotrvajících certifikátů.
- Vyžaduje se obnovení.  Certifikát budete muset prodloužit během životnosti zařízení. Délka platnosti certifikátu závisí na kontextu a budete potřebovat strategii pro obnovení.  Strategie by měla zahrnovat, kde se certifikáty připravují, a jaký typ špičkových funkcí, které vaše zařízení musí používat v procesu obnovení. 

### <a name="when-to-generate-certificates"></a>Kdy generovat certifikáty
Možnosti připojení k internetu ve vaší továrně budou mít vliv na proces generování certifikátů. Pro generování certifikátů máte několik možností: 

- Předem načtené certifikáty.  Někteří dodavatelé HSM nabízejí službu Premium, ve které dodavatel HSM instaluje certifikáty pro zákazníka. Za prvé budou mít zákazníci přístup k podpisovým certifikátům od dodavatele HSM. Potom dodavatel HSM nainstaluje certifikáty podepsané tímto podpisovým certifikátem na každý modul HSM, který zákazník koupí. Veškerý zákazník musí provést instalaci modulu HSM na zařízení. I když tato služba přidává náklady, pomáhá zjednodušit proces výroby.  A řeší otázku Instalace certifikátů.
- Certifikáty generované zařízením.  Pokud vaše zařízení vygenerovala certifikáty interně, musíte z něj extrahovat veřejný certifikát X. 509, aby ho bylo možné zaregistrovat v DPS. 
- Propojená továrna.  Pokud má vaše továrna připojení, můžete vygenerovat certifikáty zařízení, kdykoli je budete potřebovat.
- Objekt pro vytváření offline s vlastní infrastrukturou veřejných klíčů. Pokud vaše továrna nemá připojení a používáte vlastní infrastrukturu veřejných klíčů s podporou offline, můžete certifikáty vygenerovat, až je budete potřebovat.
- Objekt pro vytváření offline s infrastrukturou veřejných klíčů třetích stran. Pokud vaše továrna nemá připojení a používáte infrastrukturu veřejných klíčů třetí strany, musíte certifikáty vygenerovat před časem. A bude nutné vygenerovat certifikáty z umístění, které má připojení. 

### <a name="when-to-install-certificates"></a>Kdy nainstalovat certifikáty
Po vygenerování certifikátů pro zařízení IoT je můžete nainstalovat do zařízení. 

Pokud používáte předem načtené certifikáty s modulem HARDWAROVÉho zabezpečení, proces je zjednodušený. Po nainstalování modulu HARDWAROVÉho zabezpečení do zařízení k němu může přistupovat kód zařízení. Pak zavoláte rozhraní API HSM pro přístup k certifikátu, který je uložený v modulu HARDWAROVÉho zabezpečení. Tento přístup je nejpohodlnější pro váš výrobní proces. 

Pokud nepoužíváte předem načtený certifikát, je nutné nainstalovat certifikát jako součást produkčního procesu. Nejjednodušší je postup, jak nainstalovat certifikát do modulu HSM ve stejnou dobu jako počáteční image firmwaru. Váš proces musí přidat krok pro instalaci image do každého zařízení. Po provedení tohoto kroku můžete před zabalením a odesláním zařízení spustit konečné kontroly kvality a všechny další kroky. 

K dispozici jsou softwarové nástroje, díky kterým můžete spustit proces instalace a konečnou kontrolu kvality v jednom kroku. Tyto nástroje můžete upravit pro vygenerování certifikátu nebo pro vyžádání certifikátu z předem vygenerovaného úložiště certifikátů. Software pak může nainstalovat certifikát tam, kde ho potřebujete nainstalovat. Softwarové nástroje tohoto typu umožňují provozovat produkční produkci ve velkém měřítku. 

Po nainstalování certifikátů na vaše zařízení se v dalším kroku dozvíte, jak zaregistrovat zařízení pomocí [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integrace čipu TPM do výrobního procesu
Pokud k ověření zařízení IoT používáte čip TPM, Tato část nabízí doprovodné materiály. Tato příručka se zabývá široce používanými zařízeními TPM 2,0, která mají podporu klíčů HMAC (hash-based Message Authentication Code). Specifikace TPM pro čipy TPM je standard ISO, který je spravovaný skupinou důvěryhodných výpočtů. Další informace o TPM najdete v článku specifikace pro [tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) a [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Převzetí vlastnictví čipu TPM
Důležitým krokem při výrobě zařízení s čipem TPM je převzít vlastnictví čipu TPM. Tento krok je povinný, abyste mohli zadat klíč pro vlastníka zařízení. Prvním krokem je extrakce ověřovacího klíče (EK) ze zařízení. Dalším krokem je skutečné nároky na vlastnictví. Způsob, jakým dosáhnete, závisí na tom, který čip TPM a operační systém používáte. V případě potřeby se obraťte na výrobce čipu TPM nebo vývojáře operačního systému zařízení a určete, jak se má vlastnictví vyžádat. 

V rámci výrobního procesu můžete extrahovat EK a vlastnictví deklarací v různých časech, což zvyšuje flexibilitu. Řada výrobců využívá tuto flexibilitu přidáním modulu hardwarového zabezpečení (HSM), který zvyšuje zabezpečení jejich zařízení. V této části najdete pokyny k extrakci EK, kdy se má vyžádat vlastnictví čipu TPM, a informace pro integraci těchto kroků do výrobní časové osy. 

> [!IMPORTANT]
> Následující pokyny předpokládají použití diskrétního, firmwaru nebo integrovaného čipu TPM. Na místech, kde je to možné, doprovodné materiály přidávají poznámky k používání nediskrétního nebo softwarového čipu TPM. Pokud používáte čip TPM softwaru, může docházet k dalším krokům, které tyto pokyny nezahrnují. Software čipy TPM má různé implementace, které překračují rámec tohoto článku.  Obecně je možné integrovat softwarový čip TPM do následující obecné výrobní Timeline. I když je ale software Emulovaný čipem TPM vhodný pro vytváření prototypů a testování, nemůže poskytovat stejnou úroveň zabezpečení jako diskrétní, firmware nebo integrovaný čip TPM. Obecně se vyhnete používání softwarového čipu TPM v produkčním prostředí.

### <a name="general-manufacturing-timeline"></a>Obecná výrobní časová osa
Následující časová osa ukazuje, jak čip TPM projde výrobním procesem a končí v zařízení. Jednotlivé výrobní procesy jsou jedinečné a tato časová osa zobrazuje nejběžnější vzory. Časová osa nabízí pokyny k provedení určitých akcí s klíči. 

#### <a name="step-1-tpm-is-manufactured"></a>Krok 1: vyrobení čipu TPM
- Pokud si koupíte čipy TPM od výrobce pro použití v zařízeních, přečtěte si téma, jestli se za vás odbalí veřejné ověřovací klíče (EK_pubs). Je užitečné, pokud výrobce poskytuje seznam EK_pubs s dodanými zařízeními. 
    > [!NOTE]
    > Výrobci čipu TPM můžete poskytnout přístup k zápisu do seznamu registrací pomocí zásad sdíleného přístupu ve vaší službě zřizování.  Tento přístup umožňuje přidat čipy TPM do seznamu registrací za vás.  Ale to je brzy v procesu výroby a vyžaduje důvěryhodnost v výrobci čipu TPM. Udělejte to na vlastní riziko. 

- Pokud vytváříte čipy TPM k prodeji výrobcům zařízení, zvažte, že zákazníkům poskytnete seznam EK_pubs společně s jejich fyzickou čipy TPM.  Poskytováním zákazníkům s EK_pubs uložíte krok do svého procesu. 
- Pokud vytváříte čipy TPM pro použití s vlastními zařízeními, identifikujte, který bod v procesu je nejpohodlnější pro extrakci EK_pub. EK_pub můžete extrahovat na kterýkoli ze zbývajících bodů na časové ose. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Krok 2: TPM se instaluje do zařízení.
V tomto okamžiku v produkčním procesu byste měli znát, se kterou instancí DPS se zařízení bude používat. V důsledku toho můžete přidat zařízení do seznamu registrace pro Automatické zřizování. Další informace o automatickém zřizování zařízení najdete v dokumentaci k [DPS](about-iot-dps.md).
- Pokud jste EK_pub neextrahovali, teď je to dobrý čas. 
- V závislosti na procesu instalace čipu TPM je tento krok také vhodným časem pro převzetí vlastnictví čipu TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Krok 3: zařízení má nainstalovaný firmware a software.
V tuto chvíli procesu nainstalujte klienta DPS spolu s rozsahem ID a globální adresou URL pro zřizování.
- Nyní je poslední pravděpodobnost, EK_pub extrakci. Pokud třetí strana nainstaluje software do vašeho zařízení, je vhodné nejprve extrahovat EK_pub.
- Tento bod výrobního procesu je ideální pro převzetí vlastnictví čipu TPM.  
    > [!NOTE]
    > Pokud používáte čip TPM softwaru, můžete ho hned nainstalovat.  Extrahujte EK_pub ve stejnou dobu.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Krok 4: zařízení je zabaleno a odesláno do skladu.
Zařízení může být ve skladu po dobu 6-12 měsíců, než se nasadí. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Krok 5: zařízení se instaluje do umístění.
Jakmile zařízení dorazí do konečného umístění, provede to prostřednictvím automatizovaného zřizování s DPS.

Další informace najdete v tématu [zřizování](about-iot-dps.md#provisioning-process) a [ověření identity čipem TPM](concepts-tpm-attestation.md). 

## <a name="resources"></a>Zdroje informací

Kromě doporučených postupů zabezpečení v tomto článku poskytuje Azure IoT prostředky, které vám pomůžou vybrat zabezpečený hardware a vytvářet zabezpečená nasazení IoT: 
- [Doporučení zabezpečení](../iot-fundamentals/security-recommendations.md) Azure IoT, která vám pomohou s procesem nasazení. 
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) nabízí službu, která vám pomůže vytvořit zabezpečená nasazení IoT. 
- Nápovědu k vyhodnocení vašeho hardwarového prostředí najdete v dokumentu White Paper [vyhodnocení zabezpečení IoT](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Nápovědu k výběru zabezpečeného hardwaru najdete v tématu [správný zabezpečený hardware pro nasazení IoT](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 