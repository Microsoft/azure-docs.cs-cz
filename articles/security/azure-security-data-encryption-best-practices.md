---
title: Osvědčené postupy šifrování a zabezpečení dat | Dokumentace Microsoftu
description: Tento článek poskytuje sadu osvědčených postupů pro zabezpečení dat a šifrování pomocí integrovaných v možnosti Azure.
services: security
documentationcenter: na
author: barclayn
manager: mbalwin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: barclayn
ms.openlocfilehash: 686d4a8ac5239af12206b57072cc00aa10114d79
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976507"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Doporučené postupy pro zabezpečení dat v Azure a šifrování

K ochraně dat v cloudu, budete muset počítat možné stavy, v nichž může dojít, vaše data a jaké ovládací prvky jsou k dispozici pro tento stav. Osvědčené postupy pro zabezpečení dat v Azure a šifrování se týkají následující stavy dat:

- V klidovém stavu: To zahrnuje všechny objekty úložiště informace, kontejnery a typy, které existují staticky na fyzickém médiu, zda magnetické nebo optické disk.
- Při přenosu: Data se přenášejí mezi komponentami, umístění nebo programy, je při přenosu. Příklady jsou přenos v síti v rámci služby Service bus (z místního na cloud (a naopak), včetně hybridních připojení, jako je například ExpressRoute), nebo během vstupně výstupní.

V tomto článku se budeme zabývat kolekce dat Azure zabezpečení a šifrování osvědčené postupy. Tyto osvědčené postupy jsou odvozeny z našich zkušeností s Azure data zabezpečení a šifrování a prostředí zákazníků, jako sami.

Pro každý osvědčeným postupem je vysvětlíme:

* Jaký je doporučený postup
* Proč chcete povolit tento osvědčený postup
* Pokud chcete povolit osvědčený postup, co mohou být způsobeny
* Je to možné alternativy doporučené postupy
* Jak lze zjistíte, jak povolit osvědčený postup

Tento článek osvědčené postupy šifrování a zabezpečení dat Azure vychází stanovisko shody a možnostech platformy Azure a sady funkcí, protože existují v okamžiku, kdy byla zapsána v tomto článku. Názory a technologie v průběhu času měnit a tento článek bude aktualizován v pravidelných intervalech, aby tyto změny projevily.

## <a name="choose-a-key-management-solution"></a>Výběr řešení správy klíčů

Ochrana klíče je nezbytné pro ochranu vašich dat v cloudu.

[Služba Azure Key Vault](../key-vault/key-vault-overview.md) pomáhá chránit kryptografické klíče a tajné kódy, které cloudové aplikace a služby používat. Key Vault zjednodušuje proces správy klíčů a zajišťuje vám kontrolu nad klíči, které se používají k přístupu a šifrování dat. Vývojářům můžete vytvořit klíče pro vývoj a testování během několika minut a pak je migrujte na produkční klíče. Správci zabezpečení můžou klíčům podle potřeby udělovat (a odvolávat) oprávnění.

Key Vault můžete vytvořit několik zabezpečených kontejnerů označovaných jako trezory. Tyto trezory využívají moduly hardwarového zabezpečení. Trezory pomáhají snížit riziko nechtěné ztráty informací o zabezpečení tím, že centralizují ukládání tajných klíčů aplikací. Trezory klíčů také řídí a protokolují přístup k ničemu jsou v nich uložené. Azure Key Vault může zpracovávat žádosti o a obnovují se certifikáty zabezpečení TLS (Transport Layer). Poskytuje funkce pro robustní řešení pro správu životního cyklu certifikátu.

Azure Key Vault je navržený pro podporu a tajných klíčů aplikací. Key Vault není určená jako úložiště uživatelských hesel.

Toto jsou osvědčené postupy zabezpečení pro používání služby Key Vault.

**Osvědčený postup**: Udělení přístupu uživatelům, skupinám a aplikacím v konkrétním oboru.   
**Podrobnosti o**: Pomocí předdefinovaných rolí pro RBAC. K udělení přístupu uživatelům ke správě trezorů klíčů, by například přiřadit předdefinovanou roli [Přispěvatel Key Vault](../role-based-access-control/built-in-roles.md) tomuto uživateli v konkrétním oboru. Rozsah v tomto případě by předplatné, skupinu prostředků nebo jenom určitý trezor klíčů. Pokud předdefinované role nevyhovují vašim potřebám, můžete si [definovat vlastní role](../role-based-access-control/custom-roles.md).

**Osvědčený postup**: Co uživatelé mají přístup k ovládacího prvku.   
**Podrobnosti o**: Přístup k trezoru klíčů je řízen prostřednictvím dvou oddělených rozhraní: rovina správy a rovina dat. Řízení přístupu roviny správy a roviny dat fungují nezávisle.

Použití RBAC pro ovládací prvek, co uživatelé mají přístup k. Pokud chcete aplikaci udělit přístup k používání klíčů v trezoru klíčů, je potřeba jenom udělit přístup k rovině dat pomocí zásady přístupu trezoru klíčů a pro tuto aplikaci je potřeba žádný přístup k rovině správy. Naopak pokud má uživatel bude moct číst vlastnosti trezoru a značky, ale nemáte přístup k klíče, tajné kódy a certifikáty, můžete udělit přístup pro čtení tohoto uživatele pomocí RBAC a není přístup k rovině dat je povinný.

**Osvědčený postup**: Store certifikáty v trezoru klíčů. Certifikáty jsou vysokou hodnotu. Do nesprávných rukou může být ohrožena zabezpečení vaší aplikace nebo zabezpečení vašich dat.   
**Podrobnosti o**: Azure Resource Manageru můžete bezpečně nasadit certifikáty uložené v Azure Key Vault a virtuální počítače Azure nasazené virtuální počítače. Nastavením odpovídající zásady přístupu pro trezor klíčů, můžete také řídit, kdo získá přístup k vašemu certifikátu. Další výhodou je, že můžete spravovat všechny certifikáty na jednom místě ve službě Azure Key Vault. Zobrazit [nasazení certifikátů do virtuálních počítačů z trezoru klíčů spravovaných zákazníkem](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) Další informace.

**Osvědčený postup**: Ujistěte se, že můžete obnovit odstranění trezorům klíčů nebo objektů trezoru klíčů.   
**Podrobnosti o**: Odstranění klíče trezory Recovery Services nebo objektů trezoru klíčů může být neúmyslným nebo zákeřným. Povolit obnovitelné odstranění a mazání funkce ochrany služby Key Vault, zejména pro klíče, které slouží k šifrování dat v klidovém stavu. Odstraňování těchto klíčů je ekvivalentní ztrátu dat, abyste mohli obnovit odstraněné trezory a trezor objektů v případě potřeby. Procvičte operace obnovení služby Key Vault v pravidelných intervalech.

> [!NOTE]
> Pokud má uživatel oprávnění Přispěvatel (RBAC) k rovině správy trezoru klíčů, se může udělit přístup k rovině dat tak, že nastavíte zásady přístupu trezoru klíčů. Doporučujeme vám, že je úzce řídit, kdo má přístup přispěvatele k vašim trezorům klíčů, zajistíte, že jen autorizované osoby můžete přístup a správa trezorů klíčů, klíče, tajné kódy a certifikáty.
>
>

## <a name="manage-with-secure-workstations"></a>Správa pomocí zabezpečených pracovních stanic

> [!NOTE]
> Správce předplatného nebo vlastník používejte zabezpečený přístup k pracovní stanici nebo pracovní stanice s privilegovaným přístupem.
>
>

Protože většina útoků cílí na koncové uživatele, stane se koncový bod jedním z primárních bodů útoku. Útočník zneužije koncový bod můžete použít přihlašovací údaje uživatele k získání přístupu k datům organizace. Většina útoků koncový bod využít výhod vzhledem k tomu, že uživatelé mají správci v jejich místní pracovní stanice.

**Osvědčený postup**: Použijte pracovní stanice pro zabezpečenou správu k ochraně citlivých účtů, úloh a data.   
**Podrobnosti o**: Použití [privileged access workstation](https://technet.microsoft.com/library/mt634654.aspx) pro omezení možností útoku v pracovních stanic. Tyto pracovní stanice pro zabezpečenou správu vám může pomoct zmírnit některé tyto útoky a ujistěte se, že vaše data jsou bezpečnější.

**Osvědčený postup**: Zajištění ochrany koncového bodu.   
**Podrobnosti o**: Vynutit zásady zabezpečení napříč všemi zařízeními, které se používají ke zpracování dat, bez ohledu na umístění dat (v cloudu nebo místní).

## <a name="protect-data-at-rest"></a>Ochrana dat v klidovém stavu

[Šifrování dat v klidovém stavu](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) je povinný krok suverenita dat o ochraně osobních údajů, dodržování předpisů a data.

**Osvědčený postup**: Použijte šifrování disku, která pomáhá chránit vaše data.   
**Podrobnosti o**: Použití [Azure Disk Encryption](azure-security-disk-encryption.md). Umožňuje správcům IT šifrování disků Windows a virtuálních počítačů IaaS s Linuxem. Šifrování disku kombinuje funkci Windows BitLocker standardní a funkci dm-crypt systému Linux zajišťuje šifrování pro operační systém a datové disky.

Azure Storage a Azure SQL Database šifrování neaktivních uložených dat ve výchozím nastavení a mnoho služeb nabídky šifrování jako možnost. Pro kontrolu nad klíči, které k přístupu a šifrování dat můžete použít Azure Key Vault. Zobrazit [podpora modelu šifrování poskytovatele prostředků Azure získat další](azure-security-encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Osvědčené postupy**: Šifrování pro zmírnění rizika související s neoprávněného přístupu k datům.
**Podrobnosti o**: Šifrování jednotky, než k nim napíšete citlivá data.

Organizace, které nevynucují šifrování dat jsou vystaveny více dat důvěrnosti. Například může neoprávněným nebo neautorizovaných serverů uživatelů odcizit data v ohrožením účtů nebo získání neoprávněného přístupu k datům zakódovaný ve formátu vymazat. Společnosti také musíte prokázat, že jsou pečlivé a pomocí správný bezpečnostní mechanismy pro zvýšení zabezpečení svých dat pro dosažení souladu s předpisy odvětví.

## <a name="protect-data-in-transit"></a>Ochranu přenášených dat

Ochrana dat při přenosu by měl být nedílnou součást vaší strategie ochrany dat. Protože data je přesun vpřed a zpět z mnoha umístění, obecně doporučujeme vždy používat protokoly SSL/TLS pro výměnu dat mezi různými umístěními. V některých případech můžete chtít izolovat celý komunikační kanál mezi místní a cloudové infrastruktury pomocí sítě VPN.

Pro data přesouvaná mezi vaší místní infrastruktuře i v Azure vezměte v úvahu odpovídající opatření, jako je HTTPS nebo VPN. Při posílání šifrovaného provozu mezi virtuální sítí Azure a místním umístěním přes veřejný internet, použijte [Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/).

Toto jsou osvědčené postupy, které jsou specifické pro pomocí Azure VPN Gateway, HTTPS a SSL/TLS.

**Osvědčený postup**: Zabezpečený přístup z více pracovních stanic umístěny v místním ke službě Azure virtual network.   
**Podrobnosti o**: Použití [site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

**Osvědčený postup**: Zabezpečený přístup z jednotlivých pracovní stanice místních ke službě Azure virtual network.   
**Podrobnosti o**: Použití [point-to-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

**Osvědčený postup**: Přesuňte větších datových sad vyhrazenou vysokorychlostní propojení WAN.   
**Podrobnosti o**: Použití [ExpressRoute](../expressroute/expressroute-introduction.md). Pokud se rozhodnete používat ExpressRoute, můžete taky k šifrování dat na úrovni aplikace s použitím [SSL/TLS](https://support.microsoft.com/kb/257591) nebo jiné protokoly pro zvýšení ochrany.

**Osvědčený postup**: Pracovat s Azure Storage na webu Azure portal.   
**Podrobnosti o**: Všechny transakce nastat prostřednictvím protokolu HTTPS. Můžete také použít [REST API pro Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx) přes protokol HTTPS pro interakci s [služby Azure Storage](https://azure.microsoft.com/services/storage/) a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organizace, které se nepodařilo ochranu přenášených dat budou náchylnější k [útoky man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [odposlouchávání](https://technet.microsoft.com/library/gg195641.aspx)a napadení relace. Tyto útoky může být prvním krokem v získání přístupu k důvěrným datům.

## <a name="secure-email-documents-and-sensitive-data"></a>Zabezpečení e-mailů, dokumenty a citlivá data

Chcete pro řízení a zabezpečení e-mailu, dokumenty a citlivá data, která sdílíte mimo vaši společnost. [Služba Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) je cloudové řešení, které pomáhá organizacím klasifikovat, označovat a chránit svoje dokumenty a e-maily. To můžete udělat automaticky provádět správci, kteří definují pravidla a podmínky, ručně podle uživatele nebo kombinaci, kde uživatelé získat doporučení.

Klasifikace byla vždy identifikovatelná za všech okolností, bez ohledu na to ve kterém jsou data uložená nebo s kým se sdílí. Popisky zahrnují vizuální označení, jako je například záhlaví, zápatí nebo vodoznak. Do záhlaví e-mailu ve formátu prostého textu a soubory se přidají metadata. Prostý text zajistí, že jiné služby, jako jsou například řešení, aby se zabránilo ztrátě dat, můžete klasifikaci identifikovat a provést příslušnou akci.

Technologie ochrany používá Azure Rights Management (Azure RMS). Tato technologie se integruje s jinými cloudovými službami Microsoftu a aplikace, jako je Office 365 a Azure Active Directory. Tato technologie ochrany používá zásady autorizace, identity a šifrování. Ochranu, která používá službu Azure RMS zůstává součástí dokumentů a e-mailů, bez ohledu na jejich umístění – uvnitř nebo mimo organizaci, sítích, souborových serverů a aplikací.

Toto řešení ochrany informací vám zajišťuje kontrolu nad daty, i když se sdílí s dalšími uživateli. Tyto aplikace a řešení jsou místně nebo v cloudu, můžete použít také Azure RMS s řádku obchodními aplikacemi a řešeními ochrany informací od dodavatelů softwaru.

Doporučujeme vám:

- [Nasazení služby Azure Information Protection](https://docs.microsoft.com/azure/information-protection/deployment-roadmap) pro vaši organizaci.
- Použití popisků, které odpovídají vašim podnikovým požadavkům. Příklad: Použijte popisek s názvem "vysoce důvěrné informace" na všechny dokumenty a e-maily, které obsahují top-secret data, klasifikovat a chránit tato data. Jenom Autorizovaní uživatelé pak můžete přístup k těmto datům, s libovolnými nakonfigurovanými omezeními, které zadáte.
- Konfigurace [protokolování použití pro službu Azure RMS](https://docs.microsoft.com/azure/information-protection/log-analyze-usage) tak, aby můžete sledovat, jak vaše organizace používá službu ochrany.

Organizace, které jsou na slabé [klasifikace dat](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) a ochranu souborů může být více náchylný k úniku nebo zneužití dat. S ochranou správná můžete analyzovat datové toky, chcete-li získat informace o svojí firmě, rozpoznali nebezpečné chování a podnikli nápravná opatření, sledovali přístup k dokumentům a tak dále.

## <a name="next-steps"></a>Další postup

Zobrazit [osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md) pro další doporučené postupy zabezpečení, mají použít, když jste návrhu, nasazení a správa cloudových řešení pomocí služby Azure.

Jsou následující prostředky vám poskytnou další obecné informace o zabezpečení Azure a související služby Microsoftu:
* [Blog týmu Azure zabezpečení](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější vydání v Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – tam, kde mohou být hlášeny chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo prostřednictvím e-mailu secure@microsoft.com
