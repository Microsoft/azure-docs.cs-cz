---
title: NIST SP 800-53 R4 ukázkové ovládací prvky
description: Mapování řízení ukázky podrobného plánu NIST SP 800-53 R4. Každý ovládací prvek je mapován na jednu nebo více zásad Azure, které pomáhají s hodnocením.
ms.date: 11/18/2019
ms.topic: sample
ms.openlocfilehash: 104224ec96a5989a103f4d00310cf08b684b4d67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546617"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Mapování řízení ukázky podrobného plánu NIST SP 800-53 R4

V následujícím článku podrobně popisuje, jak azure plány NIST SP 800-53 R4 ukázkový vzor mapy nist SP 800-53 R4 ovládací prvky. Další informace o ovládacích prvcích naleznete v [tématu NIST SP 800-53](https://nvd.nist.gov/800-53).

Následující mapování jsou **nist SP 800-53 (Rev. 4)** ovládací prvky. Pomocí navigace vpravo přejděte přímo na konkrétní mapování ovládacího prvku. Mnoho mapovaných ovládacích prvků se implementuje pomocí iniciativy [Zásad azure.](../../../policy/overview.md) Pokud chcete zkontrolovat celou iniciativu, otevřete **zásady** na webu Azure portal a vyberte stránku **Definice.** Potom vyhledejte a vyberte ** \[náhled\]: Auditujte ovládací prvky NIST SP 800-53 R4 a nasaďte konkrétní rozšíření virtuálních her, abyste podpořili předdefinovanou** iniciativu zásad na požadavky na audit.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definic [zásad Azure.](../../../policy/overview.md) Tyto zásady vám mohou pomoci [posoudit dodržování](../../../policy/how-to/get-compliance-data.md) ovládacího prvku; však často není 1:1 nebo úplnou shodu mezi ovládacím prvkem a jednu nebo více zásad. Jako **takový, kompatibilní** v zásadách Azure odkazuje pouze na zásady samotné; Tím nezajistíte, že jste plně kompatibilní se všemi požadavky ovládacího prvku. Kromě toho standard dodržování předpisů zahrnuje ovládací prvky, které nejsou v tuto chvíli adresovány žádnými definicemi zásad Azure. Dodržování předpisů v zásadách Azure je tedy pouze částečným zobrazením celkového stavu dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi zásad Azure pro tento ukázkový plán dodržování předpisů se může v průběhu času měnit. Chcete-li zobrazit historii změn, podívejte se na [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/nist-sp-800-53-rev4/control-mapping.md).

## <a name="ac-2-account-management"></a>Správa účtu AC-2

Tento podrobný plán vám pomůže zkontrolovat účty, které nemusí splňovat požadavky vaší organizace na správu účtů. Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které auditují externí účty s oprávněními ke čtení, zápisu a vlastníkovi u předplatných a zastaralé účty. Kontrolou účtů auditovaných těmito zásadami můžete podniknout příslušné kroky k zajištění splnění požadavků na správu účtu.

- Zastaralé účty by měly být odebrány z vašeho předplatného
- Zastaralé účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného
- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Z předplatného by měly být odebrány externí účty s oprávněním ke čtení.
- Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Správa účtů | Schémata založená na rolích

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC), které vám pomohou spravovat, kdo má přístup k prostředkům v Azure. Pomocí portálu Azure můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán také přiřazuje definice [zásad Azure](../../../policy/overview.md) auditování použití ověřování Azure Active Directory pro servery SQL a service fabric. Použití ověřování Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databází a dalších služeb Microsoftu. Kromě toho tento podrobný plán přiřadí definici zásad Azure k auditování použití vlastních pravidel RBAC. Pochopení, kde jsou implementována vlastní pravidla RBAC vám může pomoci ověřit potřeby a správné implementace, jako vlastní Pravidla RBAC jsou náchylné k chybám.

- Správce služby Azure Active Directory by měl být zřízen pro servery SQL.
- Auditování použití vlastních pravidel RBAC
- Clustery Service Fabric by měly používat azure active directory jenom pro ověřování klientů.

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Správa účtů | Monitorování účtů / Atypické použití

Přístup k virtuálnímu počítači just-in-time (JIT) uzamkne příchozí provoz na virtuálních počítačích Azure, čímž snižuje vystavení útokům a zároveň poskytuje snadný přístup k virtuálním počítačům v případě potřeby. Všechny požadavky JIT pro přístup k virtuálním počítačům jsou zaznamenány v protokolu aktivit, což umožňuje sledovat atypické použití. Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže monitorovat virtuální počítače, které podporují přístup just-in-time, ale ještě nebyly nakonfigurované.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Vynucování toku informací

Sdílení prostředků mezi zdroji (CORS) může povolit, aby byly prostředky služby App Services požadovány z vnější domény. Společnost Microsoft doporučuje povolit interakci s rozhraním API, funkcí a webovými aplikacemi pouze požadovaným doménám. Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže monitorovat omezení přístupu k prostředkům CORS v Centru zabezpečení Azure.
Principy implementací CORS vám mohou pomoci ověřit, že jsou implementovány ovládací prvky toku informací.

- CORS by neměl povolit přístup ke webové aplikaci pro každý prostředek.

## <a name="ac-5-separation-of-duties"></a>AC-5 Oddělení cel

Mít jenom jednoho vlastníka předplatného Azure neumožňuje redundanci správy. Naopak s příliš mnoho vlastníků předplatného Azure můžete zvýšit potenciál pro porušení prostřednictvím ohroženého účtu vlastníka. Tento podrobný plán vám pomůže udržovat odpovídající počet vlastníků předplatného Azure přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují počet vlastníků pro předplatná Azure. Tento podrobný plán také přiřazuje definice zásad Azure, které vám pomohou řídit členství ve skupině Administrators na virtuálních počítačích s Windows. Správa oprávnění vlastníka předplatného a oprávnění správce virtuálních strojů vám může pomoci implementovat vhodné oddělení povinností.

- Pro vaše předplatné by měli být určeni maximálně 3 vlastníci.
- Auditovat virtuální servery systému Windows, ve kterých skupina Administrators obsahuje některý ze zadaných členů
- Auditovat virtuální servery systému Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Požadavky na nasazení auditovat virtuální uživatele systému Windows, ve kterých skupina Administrators obsahuje některý ze zadaných členů
- Nasadit požadavky na auditovat virtuální chod windows, ve kterém skupina Administrators neobsahuje všechny zadané členy
- K vašemu předplatnému by mělo být přiřazeno více než jednoho vlastníka.

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) Nejmenší oprávnění | Kontrola uživatelských oprávnění

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC), které vám pomohou spravovat, kdo má přístup k prostředkům v Azure. Pomocí portálu Azure můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřazuje definice [zásad Azure](../../../policy/overview.md) k účtům auditování, které by měly být upřednostněny pro kontrolu. Kontrola těchto ukazatelů účtu vám může pomoci zajistit implementaci nejméně privilegovaných ovládacích prvků.

- Pro vaše předplatné by měli být určeni maximálně 3 vlastníci.
- Auditovat virtuální servery systému Windows, ve kterých skupina Administrators obsahuje některý ze zadaných členů
- Auditovat virtuální servery systému Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Požadavky na nasazení auditovat virtuální uživatele systému Windows, ve kterých skupina Administrators obsahuje některý ze zadaných členů
- Nasadit požadavky na auditovat virtuální chod windows, ve kterém skupina Administrators neobsahuje všechny zadané členy
- K vašemu předplatnému by mělo být přiřazeno více než jednoho vlastníka.

## <a name="ac-16-security-attributes"></a>Atributy zabezpečení AC-16

Funkce zjišťování a klasifikace dat pokročilého zabezpečení dat pro Azure SQL Database poskytuje funkce pro zjišťování, klasifikaci, označování a ochranu citlivých dat ve vašich databázích. Může sloužit k poskytování přehledu o stavu klasifikace databáze a ke sledování přístupu k citlivým datům v databázi i mimo ni. Rozšířené zabezpečení dat vám může pomoci zajistit informace spojené s příslušnými atributy zabezpečení pro vaši organizaci. Tento podrobný plán přiřazuje definice [zásad Azure](../../../policy/overview.md) ke sledování a vynucení využití rozšířeného zabezpečení dat na serveru SQL. 

- U spravovaných instancí by mělo být povoleno rozšířené zabezpečení dat.
- Na serverech SQL by mělo být povoleno rozšířené zabezpečení dat.
- Nasazení rozšířeného zabezpečení dat na serverech SQL

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>Vzdálený přístup AC-17 (1) | Automatizované monitorování / řízení

Tento podrobný plán vám pomůže monitorovat a řídit vzdálený přístup přiřazením definic [zásad Azure](../../../policy/overview.md) ke sledování, že je vypnuté vzdálené ladění aplikace Azure App Service. Podrobný plán také přiřazuje definice zásad, které auditují virtuální počítače Linux, které umožňují vzdálená připojení z účtů bez hesel. Podrobný plán navíc přiřadí definici zásad Azure, která vám pomůže monitorovat neomezený přístup k účtům úložiště. Sledování těchto ukazatelů vám může pomoci zajistit, aby metody vzdáleného přístupu byly v souladu s vašimi zásadami zabezpečení.

- \[Preview\]: Auditování virtuálních počítačů s Linuxem, které umožňují vzdálená připojení z účtů bez hesel
- \[Preview\]: Nasazení požadavků na auditování virtuálních počítačů s Linuxem, které umožňují vzdálená připojení z účtů bez hesel
- Auditovat neomezený přístup k účtům úložiště v síti
- Vzdálené ladění by mělo být vypnuto pro aplikaci API.
- Vzdálené ladění by mělo být vypnuto pro aplikaci Function App
- Vzdálené ladění by mělo být vypnuto pro webovou aplikaci.

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) Obsah záznamů auditu | Centralizovaná správa plánovaného obsahu záznamu auditu

Data protokolu shromážděná službou Azure Monitor se ukládají v pracovním prostoru Log Analytics, který umožňuje centralizovanou konfiguraci a správu. Tento podrobný plán vám pomůže zajistit, že se události zaznamenávají přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují a vynucují nasazení agenta Log Analytics na virtuálních počítačích Azure.

- \[Náhled\]: Nasazení agenta analýzy protokolů auditu – image virtuálního počítače (OS) není uveden jako seznam
- \[Náhled\]: Nasazení agenta analýzy protokolů auditu ve službě VMSS – image virtuálního počítače (OS) není uveden
- \[Náhled\]: Pracovní prostor analýzy protokolu auditu pro virtuální ms – neshoda sestavy
- \[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení v Linuxu (VMSS)
- \[Náhled:\]Nasazení agenta analýzy protokolů pro virtuální počítače s Linuxem
- \[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení windows (VMSS)
- \[Náhled\]: Nasazení agenta analýzy protokolů pro virtuální servery Windows

## <a name="au-5-response-to-audit-processing-failures"></a>Odpověď AU-5 na selhání zpracování auditu

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které monitorují konfigurace protokolování auditu a událostí. Sledování těchto konfigurací může poskytnout indikátor selhání nebo chybné konfigurace systému auditu a pomůže vám provést nápravná opatření.

- Auditování nastavení diagnostiky
- Auditování nastavení auditování na úrovni serveru SQL
- U spravovaných instancí by mělo být povoleno rozšířené zabezpečení dat.
- Na serverech SQL by mělo být povoleno rozšířené zabezpečení dat.

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Audit ní přezkum, analýza a podávání zpráv | Centrální přezkum a analýza

Data protokolu shromážděná službou Azure Monitor se ukládají v pracovním prostoru Log Analytics, který umožňuje centralizované vytváření sestav a analýzy. Tento podrobný plán vám pomůže zajistit, že se události zaznamenávají přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují a vynucují nasazení agenta Log Analytics na virtuálních počítačích Azure.

- \[Náhled\]: Nasazení agenta analýzy protokolů auditu – image virtuálního počítače (OS) není uveden jako seznam
- \[Náhled\]: Nasazení agenta analýzy protokolů auditu ve službě VMSS – image virtuálního počítače (OS) není uveden
- \[Náhled\]: Pracovní prostor analýzy protokolu auditu pro virtuální ms – neshoda sestavy
- \[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení v Linuxu (VMSS)
- \[Náhled:\]Nasazení agenta analýzy protokolů pro virtuální počítače s Linuxem
- \[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení windows (VMSS)
- \[Náhled\]: Nasazení agenta analýzy protokolů pro virtuální servery Windows

## <a name="au-12-audit-generation"></a>Generování auditu AU-12

Tento podrobný plán vám pomůže zajistit, aby se systémové události zaznamenávalo přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují nastavení protokolu prostředků Azure. Tyto definice zásad auditují a vynucují nasazení agenta Log Analytics na virtuálních počítačích Azure a konfiguraci nastavení auditu pro jiné typy prostředků Azure. Tyto definice zásad také audit konfigurace diagnostických protokolů poskytnout přehled o operacích, které se provádějí v rámci prostředků Azure. Auditování a rozšířené zabezpečení dat jsou navíc konfigurovány na serverech SQL.

- \[Náhled\]: Nasazení agenta analýzy protokolů auditu – image virtuálního počítače (OS) není uveden jako seznam
- \[Náhled\]: Nasazení agenta analýzy protokolů auditu ve službě VMSS – image virtuálního počítače (OS) není uveden
- \[Náhled\]: Pracovní prostor analýzy protokolu auditu pro virtuální ms – neshoda sestavy
- \[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení v Linuxu (VMSS)
- \[Náhled:\]Nasazení agenta analýzy protokolů pro virtuální počítače s Linuxem
- \[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení windows (VMSS)
- \[Náhled\]: Nasazení agenta analýzy protokolů pro virtuální servery Windows
- Auditování nastavení diagnostiky
- Auditování nastavení auditování na úrovni serveru SQL
- U spravovaných instancí by mělo být povoleno rozšířené zabezpečení dat.
- Na serverech SQL by mělo být povoleno rozšířené zabezpečení dat.
- Nasazení rozšířeného zabezpečení dat na serverech SQL
- Nasazení auditování na serverech SQL
- Nasazení nastavení diagnostiky pro skupiny zabezpečení sítě

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Nejmenší funkčnost | Zabránit spuštění programu

Adaptivní řízení aplikací v Azure Security Center je inteligentní, automatizované komplexní řešení whitelisting aplikací, které může blokovat nebo zabránit spuštění konkrétního softwaru na vašich virtuálních počítačích. Řízení aplikací lze spustit v režimu vynucení, který zakazuje spuštění neschválené aplikace. Tento podrobný plán přiřadí definici zásad Azure, která vám pomůže monitorovat virtuální počítače, kde se doporučuje seznam povolených aplikací, ale ještě nebyl nakonfigurovaný.

- Adaptivní ovládací prvky aplikací by měly být povoleny na virtuálních počítačích

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Nejnižší funkčnost | Autorizovaný software / Whitelisting

Adaptivní řízení aplikací v Azure Security Center je inteligentní, automatizované komplexní řešení whitelisting aplikací, které může blokovat nebo zabránit spuštění konkrétního softwaru na vašich virtuálních počítačích. Řízení aplikací vám pomůže vytvořit schválené seznamy aplikací pro vaše virtuální počítače. Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže monitorovat virtuální počítače, kde se doporučuje seznam povolených aplikací, ale ještě nebyl nakonfigurovaný.

- Adaptivní ovládací prvky aplikací by měly být povoleny na virtuálních počítačích

## <a name="cm-11-user-installed-software"></a>CM-11 Uživatelem nainstalovaný software

Adaptivní řízení aplikací v Azure Security Center je inteligentní, automatizované komplexní řešení whitelisting aplikací, které může blokovat nebo zabránit spuštění konkrétního softwaru na vašich virtuálních počítačích. Řízení aplikací vám může pomoci vynutit a sledovat dodržování zásad omezení softwaru. Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže monitorovat virtuální počítače, kde se doporučuje seznam povolených aplikací, ale ještě nebyl nakonfigurovaný.

- Adaptivní ovládací prvky aplikací by měly být povoleny na virtuálních počítačích

## <a name="cp-7-alternate-processing-site"></a>Cp-7 alternativní zpracování webu

Azure Site Recovery replikuje úlohy spuštěné na virtuálních počítačích z primárního umístění do sekundárního umístění. Pokud dojde k výpadku v primární lokalitě, úloha selže přes sekundární umístění. Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která audituje virtuální počítače bez nakonfigurovaného zotavení po havárii. Sledování tohoto ukazatele vám může pomoci zajistit, aby byly zavedeny nezbytné pohotovostní kontroly.

- Auditování virtuálních počítačů bez nakonfigurovaného zotavení po havárii

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identifikace a autentizace (uživatelé organizace) | Přístup k privilegovaným účtům v síti

Tento podrobný plán vám pomůže omezit a řídit privilegovaný přístup přiřazením definic [zásad Azure](../../../policy/overview.md) k auditování účtů s oprávněními vlastníka nebo zápisu, která nemají povolené vícefaktorové ověřování. Vícefaktorové ověřování pomáhá zabezpečit účty i v případě, že dojde k ohrožení jedné části ověřovacích informací. Sledováním účtů bez povoleného vícefaktorového ověřování můžete identifikovat účty, u kterých může být větší pravděpodobnost ohrožení zabezpečení.

- Vícefaktorové informace by měly být povoleny u účtů s oprávněními vlastníka k vašemu předplatnému.
- Vícefaktorové informace by měly být povoleny u účtů s oprávněními k zápisu v rámci vašeho předplatného.

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identifikace a autentizace (uživatelé organizace) | Přístup k síti k neprivilegovaným účtům

Tento podrobný plán vám pomůže omezit a řídit přístup přiřazením definice [zásad Azure](../../../policy/overview.md) k účtům auditování s oprávněními ke čtení, která nemají povolené vícefaktorové ověřování. Vícefaktorové ověřování pomáhá zabezpečit účty i v případě, že dojde k ohrožení jedné části ověřovacích informací. Sledováním účtů bez povoleného vícefaktorového ověřování můžete identifikovat účty, u kterých může být větší pravděpodobnost ohrožení zabezpečení.

- Vícefaktorové informace by měly být povoleny u účtů s oprávněními ke čtení v rámci vašeho předplatného.

## <a name="ia-5-authenticator-management"></a>Správa autentičů IA-5

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které auditují virtuální počítače s Linuxem, které umožňují vzdálená připojení z účtů bez hesel nebo mají nastavená nesprávná oprávnění v souboru passwd. Tento podrobný plán také přiřazuje definice zásad, které auditují konfiguraci typu šifrování hesel pro virtuální počítače s Windows. Sledování těchto ukazatelů pomáhá zajistit, aby ověřovateteeteeteeté systémy splňovaly zásady identifikace a ověřování vaší organizace.

- \[Náhled\]: Auditování virtuálních počítačů s Linuxem, které nemají oprávnění k souboru passwd nastavená na 0644
- \[Preview\]: Auditování virtuálních počítačů s Linuxem, které mají účty bez hesel
- \[Náhled\]: Auditování virtuálních počítače se systémem Windows, které neukládají hesla pomocí reverzibilního šifrování
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítačů s Linuxem, které nemají oprávnění k souboru passwd nastavená na 0644
- \[Preview\]: Nasazení požadavků na auditování virtuálních počítačů s Linuxem, které mají účty bez hesel
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítače se systémem Windows, které neukládají hesla pomocí reverzibilního šifrování

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Správa autentičů | Ověřování pomocí hesla

Tento podrobný plán pomáhá vynutit silná hesla přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují virtuální počítače s Windows, které nevynucují minimální sílu a další požadavky na hesla. Povědomí o virtuálních počítačích v rozporu se zásadami síly hesla vám pomůže přijmout nápravná opatření k zajištění hesla pro všechny uživatelské účty virtuálních počítačů v souladu se zásadami hesla vaší organizace.

- \[Náhled\]: Auditování virtuálních počítače se systémem Windows, které umožňují opakované použití předchozích 24 hesel
- \[Náhled\]: Auditovat virtuální počítače se systémem Windows, které nemají maximální stáří hesla 70 dnů
- \[Náhled\]: Auditovat virtuální počítače se systémem Windows, které nemají minimální stáří hesla 1 den
- \[Náhled\]: Auditujte virtuální počítače se systémem Windows, u kterých není povoleno nastavení složitosti hesla.
- \[Náhled\]: Auditovat virtuální počítače se systémem Windows, které neomezují minimální délku hesla na 14 znaků
- \[Náhled\]: Auditování virtuálních počítače se systémem Windows, které neukládají hesla pomocí reverzibilního šifrování
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítače se systémem Windows, které umožňují opakované použití předchozích 24 hesel
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítače se systémem Windows, které nemají maximální stáří hesla 70 dní
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítače se systémem Windows, které nemají minimální stáří hesla 1 den
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítače se systémem Windows, u kterých není povoleno nastavení složitosti hesla
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítače se systémem Windows, které neomezují minimální délku hesla na 14 znaků
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítače se systémem Windows, které neukládají hesla pomocí reverzibilního šifrování

## <a name="ra-5-vulnerability-scanning"></a>Prohledávání chyb zabezpečení RA-5

Tento podrobný plán vám pomůže spravovat slabá místa informačního systému přiřazením definic [zásad Azure,](../../../policy/overview.md) které monitorují slabá místa operačního systému, chyby zabezpečení SQL a chyby zabezpečení virtuálních strojů v Centru zabezpečení Azure. Azure Security Center poskytuje funkce pro vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. Tento podrobný plán také přiřazuje definice zásad, které auditují a vynucují rozšířené zabezpečení dat na serverech SQL. Pokročilé zabezpečení dat zahrnovalo posouzení zranitelnosti a pokročilé možnosti ochrany před hrozbami, které vám pomohou porozumět chybám zabezpečení nasazených prostředků.

- U spravovaných instancí by mělo být povoleno rozšířené zabezpečení dat.
- Na serverech SQL by mělo být povoleno rozšířené zabezpečení dat.
- Nasazení rozšířeného zabezpečení dat na serverech SQL
- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.
- Chyby zabezpečení v konfiguraci zabezpečení na virtuálních počítačích by měly být opraveny
- Chyby zabezpečení v databázích SQL by měly být opraveny
- Chyby zabezpečení by měly být opraveny řešením pro posouzení zranitelnosti

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Ochrana proti odmítnutí služby

Standardní úroveň distribuovaného odmítnutí služby Azure (DDoS) poskytuje další funkce a možnosti zmírnění rizik a zabezpečení nad základní úrovní služby. Mezi tyto další funkce patří integrace Azure Monitor a možnost kontrolovat zprávy o zmírnění po útoku. Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která audituje, pokud je povolena úroveň standardU DDoS. Pochopení rozdílu ve schopnostech mezi úrovněmi služeb vám může pomoci vybrat nejlepší řešení pro řešení ochrany odmítnutí služby pro vaše prostředí Azure.

- DDoS Protection Standard by měla být povolena

## <a name="sc-7-boundary-protection"></a>SC-7 Ochrana hranic

Tento podrobný plán vám pomůže spravovat a řídit hranice systému přiřazením definice [zásad Azure,](../../../policy/overview.md) která monitoruje doporučení pro posílení zabezpečení sítě v Centru zabezpečení Azure. Azure Security Center analyzuje vzorce provozu virtuálních počítačů směřujících k Internetu a poskytuje doporučení pravidel skupiny zabezpečení sítě, aby se snížil potenciální prostor pro útok.
Kromě toho tento podrobný plán také přiřazuje definice zásad, které monitorují nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněny bránou firewall, a účty úložiště s neomezeným přístupem mohou umožnit nezamýšlený přístup k informacím obsaženým v informačním systému.

- Pravidla skupiny zabezpečení sítě pro virtuální počítače orientované na Internet by měla být posílena
- Přístup přes koncový bod směřující k Internetu by měl být omezen
- Pravidla nsg pro webové aplikace na IaaS by měla být posílena
- Auditovat neomezený přístup k účtům úložiště v síti

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Ochrana hranic | Přístupové body

Přístup k virtuálnímu počítači just-in-time (JIT) uzamkne příchozí provoz na virtuálních počítačích Azure, čímž snižuje vystavení útokům a zároveň poskytuje snadný přístup k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT vám pomůže omezit počet externích připojení k vašim prostředkům v Azure. Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže monitorovat virtuální počítače, které podporují přístup just-in-time, ale ještě nebyly nakonfigurované.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Ochrana hranic | Externí telekomunikační služby

Přístup k virtuálnímu počítači just-in-time (JIT) uzamkne příchozí provoz na virtuálních počítačích Azure, čímž snižuje vystavení útokům a zároveň poskytuje snadný přístup k virtuálním počítačům v případě potřeby. Přístup k virtuálnímu počítači JIT pomáhá spravovat výjimky z vašich zásad toku provozu tím, že usnadňuje procesy žádosti o přístup a schvalování. Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže monitorovat virtuální počítače, které podporují přístup just-in-time, ale ještě nebyly nakonfigurované.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Důvěrnost a integrita přenosu | Kryptografická nebo alternativní fyzická ochrana

Tento podrobný plán pomáhá chránit důvěrné a integritu přenášených informací přiřazením definic [zásad Azure,](../../../policy/overview.md) které vám pomohou monitorovat kryptografický mechanismus implementovaný pro komunikační protokoly. Zajištění správné hošifrování komunikace vám může pomoci splnit požadavky vaší organizace nebo chránit informace před neoprávněným zveřejněním a úpravami.

- Aplikace API by měla být přístupná pouze přes protokol HTTPS
- Auditování webových serverů systému Windows, které nepoužívají zabezpečené komunikační protokoly
- Nasazení požadavků na audit webových serverů systému Windows, které nepoužívají zabezpečené komunikační protokoly
- Aplikace function by měla být přístupná pouze přes protokol HTTPS
- Měla by být povolena pouze zabezpečená připojení k mezipaměti Redis.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Webová aplikace by měla být přístupná pouze přes protokol HTTPS.

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Ochrana informací v klidu | Kryptografická ochrana

Tento podrobný plán vám pomůže vynutit zásady používání ovládacích prvků kryptografu k ochraně informací v klidovém stavu přiřazením definic [zásad Azure,](../../../policy/overview.md) které vynucují specifické ovládací prvky kryptografů a auditují použití slabých kryptografických nastavení. Pochopení, kde vaše prostředky Azure může mít neoptimální kryptografické konfigurace vám může pomoci provést nápravná opatření k zajištění prostředků jsou nakonfigurovány v souladu s vaší zásady zabezpečení informací. Konkrétně definice zásad přiřazené tímto plánem vyžadují šifrování pro účty úložiště datových jezer; vyžadovat transparentní šifrování dat v databázích SQL; a auditujete chybějící šifrování v databázích SQL, discích virtuálních počítačů a proměnných účtů automatizace.

- U spravovaných instancí by mělo být povoleno rozšířené zabezpečení dat.
- Na serverech SQL by mělo být povoleno rozšířené zabezpečení dat.
- Nasazení rozšířeného zabezpečení dat na serverech SQL
- Nasazení transparentního šifrování dat SQL DB
- Šifrování disku by mělo být použito na virtuálních počítačích.
- Vyžadovat šifrování na účtech Data Lake Store
- Transparentní šifrování dat v databázích SQL by mělo být povoleno

## <a name="si-2-flaw-remediation"></a>Si-2 Sanace chyb

Tento podrobný plán vám pomůže spravovat chyby informačního systému přiřazením definic [zásad Azure,](../../../policy/overview.md) které monitorují chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a chyby zabezpečení virtuálních strojů v Centru zabezpečení Azure. Azure Security Center poskytuje funkce pro vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase. Tento podrobný plán také přiřazuje definici zásad, která zajišťuje opravy operačního systému pro škálovací sady virtuálních strojů.

- Vyžadovat automatické opravy bitových obrázků operačního systému v škálovacích sadách virtuálních strojů
- Aktualizace systému na škálovacísady virtuálních strojů by měly být nainstalovány
- Aktualizace systému by měly být nainstalovány na virtuálních počítačích
- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.
- Chyby zabezpečení v konfiguraci zabezpečení na virtuálních počítačích by měly být opraveny
- Chyby zabezpečení v databázích SQL by měly být opraveny
- Chyby zabezpečení by měly být opraveny řešením pro posouzení zranitelnosti

## <a name="si-3-malicious-code-protection"></a>SI-3 Ochrana škodlivého kódu

Tento podrobný plán vám pomůže spravovat ochranu koncových bodů, včetně ochrany škodlivého kódu, přiřazením definic [zásad Azure,](../../../policy/overview.md) které monitorují chybějící ochranu koncových bodů na virtuálních počítačích v Azure Security Center a vynucují antimalwarové řešení Microsoftu na virtuálních počítačích s Windows.

- Nasazení výchozího rozšíření Microsoft IaaSAntimalware pro Windows Server
- Řešení ochrany koncových bodů by mělo být nainstalováno na škálovacích sadách virtuálních strojů.
- Sledování chybějící ochrany koncového bodu v Azure Security Center

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Ochrana škodlivého kódu | Centrální management

Tento podrobný plán vám pomůže spravovat ochranu koncových bodů, včetně ochrany škodlivého kódu, přiřazením definic [zásad Azure,](../../../policy/overview.md) které monitorují chybějící ochranu koncových bodů na virtuálních počítačích v Azure Security Center. Azure Security Center poskytuje funkce centralizované správy a vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase.

- Řešení ochrany koncových bodů by mělo být nainstalováno na škálovacích sadách virtuálních strojů.
- Sledování chybějící ochrany koncového bodu v Azure Security Center

## <a name="si-4-information-system-monitoring"></a>Monitorování informačního systému SI-4

Tento podrobný plán vám pomůže sledovat váš systém auditováním a vynucením protokolování a zabezpečení dat napříč prostředky Azure. Konkrétně zásady přiřazenauditování a vynucovat nasazení agenta Log Analytics a rozšířené nastavení zabezpečení pro databáze SQL, účty úložiště a síťové prostředky. Tyto funkce vám mohou pomoci zjistit neobvyklé chování a indikátory útoků, takže můžete provést příslušnou akci.

- \[Náhled\]: Nasazení agenta analýzy protokolů auditu – image virtuálního počítače (OS) není uveden jako seznam
- \[Náhled\]: Nasazení agenta analýzy protokolů auditu ve službě VMSS – image virtuálního počítače (OS) není uveden
- \[Náhled\]: Pracovní prostor analýzy protokolu auditu pro virtuální ms – neshoda sestavy
- \[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení v Linuxu (VMSS)
- \[Náhled:\]Nasazení agenta analýzy protokolů pro virtuální počítače s Linuxem
- \[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení windows (VMSS)
- \[Náhled\]: Nasazení agenta analýzy protokolů pro virtuální servery Windows
- U spravovaných instancí by mělo být povoleno rozšířené zabezpečení dat.
- Na serverech SQL by mělo být povoleno rozšířené zabezpečení dat.
- Nasazení rozšířeného zabezpečení dat na serverech SQL
- Nasazení rozšířené ochrany před hrozbami na účtech úložiště
- Nasazení auditování na serverech SQL
- Nasazení sledovacího procesu sítě při vytváření virtuálních sítí
- Nasazení zjišťování hrozeb na serverech SQL

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) Monitoring informačního systému | Analyzovat provoz / Skryté Exfiltrace

Pokročilá ochrana před internetovými hrozbami pro Azure Storage detekuje neobvyklé a potenciálně škodlivé pokusy o přístup k účtům úložiště nebo o zneužití jejich využití. Výstrahy ochrany zahrnují neobvyklé vzory přístupu, neobvyklé extrahování nebo nahrávání a podezřelou aktivitu úložiště. Tyto indikátory vám mohou pomoci odhalit skrytou exfiltraci informací.

- Nasazení rozšířené ochrany před hrozbami na účtech úložiště

> [!NOTE]
> Dostupnost konkrétních definic zásad Azure se může lišit v Azure Government a dalších národních cloudech.

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacích prvku podrobného plánu NIST SP 800-53 R4, navštivte následující články, kde se dozvíte o podrobném plánu a o tom, jak nasadit tuto ukázku:

> [!div class="nextstepaction"]
> [Podrobný plán NIST SP 800-53 R4 – přehled](./index.md)
> [podrobného plánu NIST SP 800-53 R4 – Nasazení kroků](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).
