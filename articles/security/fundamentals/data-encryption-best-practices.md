---
title: Osvědčené postupy zabezpečení a šifrování dat – Microsoft Azure
description: Tento článek poskytuje sadu osvědčených postupů pro zabezpečení a šifrování dat pomocí integrovaných funkcí Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 78f03e495ad8a02599d26a7db7e8950857a39be6
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68615782"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Osvědčené postupy šifrování a zabezpečení dat v Azure
Tento článek popisuje osvědčené postupy pro zabezpečení a šifrování dat.

Osvědčené postupy jsou založené na shodě na základě konsensu a pracují s funkcemi a sadami funkcí platformy Azure. Názory a technologie se v průběhu času mění a tento článek se pravidelně aktualizuje, aby odrážel tyto změny.

## <a name="protect-data"></a>Ochrana dat
Aby bylo možné chránit data v cloudu, musíte se přihlásit k možným stavům, ve kterých mohou být data provedena, a jaké ovládací prvky jsou pro daný stav k dispozici. Osvědčené postupy zabezpečení a šifrování dat Azure souvisejí s následujícími stavy dat:

- V klidovém umístění: To zahrnuje všechny informace o objektech úložiště, kontejnerech a typech, které existují staticky na fyzických médiích, ať už magnetickém nebo optickým diskem.
- Při přenosu: Při přenosu dat mezi součástmi, umístěními nebo programy se jedná o přenos. Příklady jsou přenosy přes síť, přes službu Service Bus (z místního prostředí do cloudu a naopak, včetně hybridních připojení, jako je ExpressRoute) nebo během procesu vstupu a výstupu.

## <a name="choose-a-key-management-solution"></a>Výběr řešení správy klíčů

Ochrana vašich klíčů je zásadní pro ochranu vašich dat v cloudu.

[Azure Key Vault](/azure/key-vault/key-vault-overview) pomáhá chránit kryptografické klíče a tajné klíče, které využívají cloudové aplikace a služby. Key Vault zjednodušuje proces správy klíčů a zajišťuje vám kontrolu nad klíči, které se používají k přístupu a šifrování dat. Vývojáři můžou vytvářet klíče pro vývoj a testování během několika minut a pak je migrovat na produkční klíče. Správci zabezpečení můžou klíčům podle potřeby udělovat (a odvolávat) oprávnění.

Key Vault můžete použít k vytvoření několika zabezpečených kontejnerů nazývaných trezory. Tyto trezory se zálohují pomocí HSM. Trezory pomáhají snížit riziko nechtěné ztráty informací o zabezpečení tím, že centralizují ukládání tajných klíčů aplikací. Trezory klíčů také řídí a protokolují přístup k cokoli uloženému v nich. Azure Key Vault může zpracovávat požadavky a obnovovat certifikáty protokolu TLS (Transport Layer Security). Poskytuje funkce pro robustní řešení pro správu životního cyklu certifikátů.

Azure Key Vault je navržena pro podporu klíčů a tajných klíčů aplikací. Key Vault není určeno jako úložiště uživatelských hesel.

Níže jsou uvedené osvědčené postupy zabezpečení pro použití Key Vault.

**Osvědčené postupy**: Udělte přístup uživatelům, skupinám a aplikacím v konkrétním oboru.   
**Podrobnosti**: Použijte předdefinované role RBAC. Pokud například chcete uživateli udělit přístup ke správě trezorů klíčů, přiřadíte předdefinovanou roli [Key Vault přispěvateli](/azure/role-based-access-control/built-in-roles) tomuto uživateli v konkrétním oboru. Rozsah v tomto případě by byl předplatné, skupina prostředků nebo jenom určitý Trezor klíčů. Pokud předdefinované role nevyhovují vašim potřebám, můžete [definovat vlastní role](/azure/role-based-access-control/custom-roles).

**Osvědčené postupy**: Řízení přístupu uživatelů k.   
**Podrobnosti**: Přístup k trezoru klíčů je řízen prostřednictvím dvou oddělených rozhraní: rovina správy a rovina dat. Řízení přístupu roviny správy a roviny dat fungují nezávisle.

Použijte RBAC k řízení přístupu uživatelů k. Pokud například chcete udělit aplikaci přístup k používání klíčů v trezoru klíčů, stačí udělit přístup k rovině dat jenom pomocí zásad přístupu trezoru klíčů a pro tuto aplikaci není potřeba přístup k rovině správy. Naopak, pokud chcete, aby uživatel mohl číst vlastnosti trezoru a značky, ale nemá přístup k klíčům, tajným klíčům nebo certifikátům, můžete tomuto uživateli udělit přístup pro čtení pomocí RBAC a není nutné mít přístup k rovině dat.

**Osvědčené postupy**: Uložte certifikáty do trezoru klíčů. Vaše certifikáty jsou vysoké hodnoty. V nesprávném případě může dojít k ohrožení zabezpečení vaší aplikace nebo zabezpečení vašich dat.   
**Podrobnosti**: Azure Resource Manager můžou bezpečně nasadit certifikáty uložené v Azure Key Vault na virtuální počítače Azure při nasazení virtuálních počítačů. Nastavením odpovídajících zásad přístupu pro Trezor klíčů také určíte, kdo získá přístup k vašemu certifikátu. Další výhodou je, že můžete spravovat všechny certifikáty na jednom místě v Azure Key Vault. Další informace najdete v tématu [nasazení certifikátů do virtuálních počítačů ze Key Vault spravovaných zákazníkem](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) .

**Osvědčené postupy**: Ujistěte se, že můžete obnovit odstranění trezorů klíčů nebo objektů trezoru klíčů.   
**Podrobnosti**: Odstranění trezorů klíčů nebo objektů trezoru klíčů může být nechtěné nebo škodlivé. Povolte funkce ochrany proti odstranění a vyprázdnění Key Vault, zejména pro klíče, které se používají k šifrování neaktivních dat. Odstranění těchto klíčů je ekvivalentní ke ztrátě dat, takže v případě potřeby můžete v případě potřeby obnovit odstraněné trezory a objekty trezoru. Praktická cvičení Key Vaultch operací obnovení.

> [!NOTE]
> Pokud má uživatel oprávnění přispěvatele (RBAC) k rovině správy trezoru klíčů, může sami udělit přístup k rovině dat nastavením zásad přístupu trezoru klíčů. Doporučujeme, abyste důkladně určili, kdo má Přispěvatel přístup k vašim trezorům klíčů, aby se zajistilo, že přístup k vašim trezorům klíčů, klíčům, tajným klíčům a certifikátům mají jenom autorizovaní uživatelé.
>
>

## <a name="manage-with-secure-workstations"></a>Správa pomocí zabezpečených pracovních stanic

> [!NOTE]
> Správce předplatného nebo vlastník by měl použít pracovní stanici zabezpečeného přístupu nebo pracovní stanici privilegovaného přístupu.
>
>

Vzhledem k tomu, že velká většina útoků cílí na koncového uživatele, se koncový bod stal jedním z primárních bodů útoku. Útočník, který nabývá tohoto koncového bodu, může pomocí přihlašovacích údajů uživatele získat přístup k datům organizace. Většina útoků na koncové body využívá skutečnost, že uživatelé jsou správci v místních pracovních stanicích.

**Osvědčené postupy**: Použijte zabezpečenou pracovní stanici pro správu k ochraně citlivých účtů, úloh a dat.   
**Podrobnosti**: [Pracovní stanici privilegovaného přístupu](https://technet.microsoft.com/library/mt634654.aspx) můžete použít ke snížení prostoru pro útoky v pracovních stanicích. Tato zabezpečená pracovní stanice pro správu vám můžou přispět k zmírnění některých těchto útoků a zajištění bezpečnosti vašich dat.

**Osvědčené postupy**: Zajistěte službu Endpoint Protection.   
**Podrobnosti**: Vyvynuťte zásady zabezpečení ve všech zařízeních, která se používají ke zpracování dat bez ohledu na umístění dat (v cloudu nebo místně).

## <a name="protect-data-at-rest"></a>Ochrana dat v klidovém umístění

[Šifrování dat v klidovém umístění](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) je povinný krok k ochraně dat, dodržování předpisů a suverenity dat.

**Osvědčené postupy**: Použijte šifrování disku pro lepší ochranu dat.   
**Podrobnosti**: Použijte [Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview). Umožňuje správcům IT šifrovat disky virtuálních počítačů s Windows a Linux IaaS. Šifrování disku kombinuje standardní funkci Windows BitLockeru a funkci Linux dm-crypt, která poskytuje šifrování svazku pro operační systém a datové disky.

Ve výchozím nastavení se Azure Storage a Azure SQL Database šifrují data v klidovém stavu a mnoho služeb nabízí šifrování jako možnost. Azure Key Vault můžete použít k zachování kontroly nad klíči, které přistupují k datům a šifrují je. Další informace najdete v tématu [Podpora modelu šifrování pro poskytovatele prostředků Azure](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Osvědčené postupy**: Pomocí šifrování můžete zmírnit rizika související s neoprávněným přístupem k datům.   
**Podrobnosti**: Zašifrujte jednotky před tím, než do nich zapíšete citlivá data.

Organizacím, které vynutily šifrování dat, jsou více vystaveny problémům s důvěrnými daty. Neoprávnění uživatelé můžou například ukrást data v ohrožených účtech nebo získat neoprávněný přístup k datům kódovaným v jasném formátu. Společnosti musí také prokázat, že jsou pečlivé vylaďování a používají správné bezpečnostní prvky pro zlepšení zabezpečení dat, aby byly dodrženy oborové předpisy.

## <a name="protect-data-in-transit"></a>Ochrana dat při přenosu

Ochrana dat při přenosu by měla být důležitou součástí vaší strategie ochrany dat. Vzhledem k tomu, že se data pohybují zpátky a zpátky z mnoha míst, doporučujeme, abyste vždy používali protokoly SSL/TLS k výměně dat napříč různými umístěními. V některých případech možná budete chtít izolovat celý komunikační kanál mezi místními a cloudovou infrastrukturou pomocí sítě VPN.

Pro pohyb dat mezi místní infrastrukturou a Azure zvažte vhodná ochranná opatření, jako je například HTTPS nebo VPN. Při posílání šifrovaného provozu mezi virtuální sítí Azure a místním umístěním přes veřejný Internet použijte [Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/).

Níže jsou uvedené osvědčené postupy, které jsou specifické pro používání Azure VPN Gateway, SSL/TLS a HTTPS.

**Osvědčené postupy**: Zabezpečený přístup z několika pracovních stanic místně do virtuální sítě Azure.   
**Podrobnosti**: Použijte [VPN typu Site-to-site](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Osvědčené postupy**: Zabezpečený přístup z jednotlivých pracovních stanic místně umístěných do virtuální sítě Azure.   
**Podrobnosti**: Použijte [VPN typu Point-to-site](/azure/vpn-gateway/vpn-gateway-point-to-site-create).

**Osvědčené postupy**: Přesuňte větší datové sady prostřednictvím vyhrazeného vysokorychlostního připojení WAN.   
**Podrobnosti**: Použijte [ExpressRoute](/azure/expressroute/expressroute-introduction). Pokud se rozhodnete používat ExpressRoute, můžete také data na úrovni aplikace šifrovat pomocí [protokolu SSL/TLS](https://support.microsoft.com/kb/257591) nebo jiných protokolů pro přidání ochrany.

**Osvědčené postupy**: Interakce s Azure Storage přes Azure Portal   
**Podrobnosti**: Všechny transakce nastávají prostřednictvím protokolu HTTPS. K interakci s [Azure Storage](https://azure.microsoft.com/services/storage/)můžete použít taky [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) přes HTTPS.

Organizace, které neumožňují ochranu dat při přenosu, jsou náchylnější k [útokům](https://technet.microsoft.com/library/gg195821.aspx)prostředníkem, [odposlouchávání](https://technet.microsoft.com/library/gg195641.aspx)a zneužití relace. Tyto útoky můžou být prvním krokem při získávání přístupu k důvěrným datům.

## <a name="secure-email-documents-and-sensitive-data"></a>Zabezpečení e-mailu, dokumentů a citlivých dat

Chcete kontrolovat a zabezpečovat e-maily, dokumenty a citlivá data, která sdílíte mimo vaši společnost. [Azure Information Protection](/azure/information-protection/) je cloudové řešení, které organizaci pomáhá klasifikovat, označovat a chránit dokumenty a e-maily. Můžete to udělat automaticky správci, kteří definují pravidla a podmínky, ručně podle uživatelů nebo kombinaci, kde uživatelé získávají doporučení.

Klasifikace je vždy identifikovatelná bez ohledu na to, kde jsou data uložená nebo s kým se sdílí. Popisky zahrnují vizuální označení, jako je záhlaví, zápatí nebo vodoznak. Do záhlaví souborů a e-mailů se přidají metadata ve formátu prostého textu. Jasný text zajišťuje, aby další služby, jako například řešení pro ochranu před únikem informací, mohly identifikovat klasifikaci a provádět příslušné akce.

Technologie ochrany používá službu Azure Rights Management (Azure RMS). Tato technologie je integrovaná do dalších cloudových služeb a aplikací Microsoftu, jako je například Office 365 a Azure Active Directory. Tato technologie ochrany používá zásady šifrování, identity a autorizace. Ochrana, která se používá prostřednictvím Azure RMS, zůstává u dokumentů a e-mailů, nezávisle na umístění – uvnitř nebo vně vaší organizace, sítí, souborových serverů a aplikací.

Toto řešení ochrany informací vám zajišťuje kontrolu nad daty, i když je sdílená s ostatními lidmi. Můžete také použít Azure RMS s vlastními obchodními aplikacemi a řešeními ochrany informací od dodavatelů softwaru, ať už jsou aplikace a řešení místní nebo v cloudu.

Doporučujeme:

- [Nasaďte Azure Information Protection](/azure/information-protection/deployment-roadmap) pro vaši organizaci.
- Použijte popisky, které odrážejí vaše obchodní požadavky. Příklad: Použijte popisek s názvem "vysoce důvěrné" na všechny dokumenty a e-maily, které obsahují data s horním tajným kódem pro klasifikaci a ochranu těchto dat. K těmto datům budou mít přístup jenom autorizovaní uživatelé, a to s libovolnými omezeními, která zadáte.
- Nakonfigurujte [protokolování využití pro Azure RMS](/azure/information-protection/log-analyze-usage) , abyste mohli sledovat, jak vaše organizace používá službu ochrany.

Organizace, které mají slabý způsob [klasifikace dat](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) a ochrany souborů, můžou být náchylné k úniku dat nebo zneužití dat. Díky správné ochraně souborů můžete analyzovat toky dat a získat tak přehled o vaší firmě, detekovat rizikové chování a provádět nápravná opatření, sledovat přístup k dokumentům a tak dále.

## <a name="next-steps"></a>Další postup

V článku [osvědčené postupy a vzory zabezpečení Azure](best-practices-and-patterns.md) najdete v tématu Doporučené postupy zabezpečení pro použití při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu pro zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější verzi v zabezpečení Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde můžou být chyby zabezpečení Microsoftu, včetně problémů s Azure, nahlášené nebo prostřednictvím e-mailu.secure@microsoft.com
