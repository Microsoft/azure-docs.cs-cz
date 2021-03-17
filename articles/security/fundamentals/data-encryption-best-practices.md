---
title: Osvědčené postupy zabezpečení a šifrování dat – Microsoft Azure
description: Tento článek poskytuje sadu osvědčených postupů pro zabezpečení a šifrování dat pomocí integrovaných funkcí Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: 414445f90c3be4c55166f6a0ecead25074d6ed74
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874109"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Osvědčené postupy šifrování a zabezpečení dat v Azure
Tento článek popisuje osvědčené postupy pro zabezpečení a šifrování dat.

Osvědčené postupy jsou založené na shodě na základě konsensu a pracují s funkcemi a sadami funkcí platformy Azure. Názory a technologie se v průběhu času mění a tento článek se pravidelně aktualizuje, aby odrážel tyto změny.

## <a name="protect-data"></a>Ochrana dat
Aby bylo možné chránit data v cloudu, musíte se přihlásit k možným stavům, ve kterých mohou být data provedena, a jaké ovládací prvky jsou pro daný stav k dispozici. Osvědčené postupy zabezpečení a šifrování dat Azure souvisejí s následujícími stavy dat:

- V klidovém formátu: to zahrnuje všechny informace o objektech úložiště, kontejnerech a typech, které existují staticky na fyzickém médiu, ať už magnetickém nebo optickým diskem.
- Při přenosu: při přenosu dat mezi součástmi, umístěními nebo programy se jedná o přenos. Příklady jsou přenosy přes síť, přes službu Service Bus (z místního prostředí do cloudu a naopak, včetně hybridních připojení, jako je ExpressRoute) nebo během procesu vstupu a výstupu.

## <a name="choose-a-key-management-solution"></a>Výběr řešení správy klíčů

Ochrana vašich klíčů je zásadní pro ochranu vašich dat v cloudu.

[Azure Key Vault](../../key-vault/general/overview.md) pomáhá chránit kryptografické klíče a tajné kódy používané cloudovými aplikacemi a službami. Key Vault zjednodušuje proces správy klíčů a zajišťuje vám kontrolu nad klíči, které se používají k přístupu a šifrování dat. Vývojáři můžou během pár minut vytvořit klíče pro vývoj a testování a potom je migrovat na produkční klíče. Správci zabezpečení můžou klíčům podle potřeby udělovat (a odvolávat) oprávnění.

Key Vault můžete použít k vytvoření několika zabezpečených kontejnerů nazývaných trezory. Tyto trezory se zálohují pomocí HSM. Trezory pomáhají snížit riziko nechtěné ztráty informací o zabezpečení tím, že centralizují ukládání tajných kódů aplikací. Trezory klíčů také řídí a protokolují přístup ke všem položkám, které jsou v nich uložené. Azure Key Vault může zpracovávat požadavky a obnovovat certifikáty protokolu TLS (Transport Layer Security). Poskytuje funkce pro robustní řešení pro správu životního cyklu certifikátů.

Azure Key Vault je navržena pro podporu klíčů a tajných klíčů aplikací. Key Vault není určeno jako úložiště uživatelských hesel.

Níže jsou uvedené osvědčené postupy zabezpečení pro použití Key Vault.

**Osvědčený postup**: udělení přístupu uživatelům, skupinám a aplikacím v konkrétním oboru.   
**Podrobnosti**: použijte předdefinované role Azure RBAC. Pokud například chcete uživateli udělit přístup ke správě trezorů klíčů, přiřadíte předdefinovanou roli [Key Vault přispěvateli](../../role-based-access-control/built-in-roles.md) tomuto uživateli v konkrétním oboru. Rozsah v tomto případě by byl předplatné, skupina prostředků nebo jenom určitý Trezor klíčů. Pokud předdefinované role nevyhovují vašim potřebám, můžete [definovat vlastní role](../../role-based-access-control/custom-roles.md).

**Osvědčený postup**: řízení přístupu uživatelů k.   
**Podrobnosti**: přístup k trezoru klíčů se ovládá přes dvě samostatná rozhraní: rovina správy a rovina dat. Řízení přístupu roviny správy a roviny dat fungují nezávisle.

Pomocí Azure RBAC můžete řídit, ke kterým uživatelům mají přístup. Pokud například chcete udělit aplikaci přístup k používání klíčů v trezoru klíčů, stačí udělit přístup k rovině dat jenom pomocí zásad přístupu trezoru klíčů a pro tuto aplikaci není potřeba přístup k rovině správy. Naopak, pokud chcete, aby uživatel mohl číst vlastnosti trezoru a značky, ale nemá přístup k klíčům, tajným klíčům nebo certifikátům, můžete tomuto uživateli udělit přístup pro čtení pomocí Azure RBAC a není nutné mít přístup k rovině dat.

**Osvědčený postup**: uložení certifikátů v trezoru klíčů. Vaše certifikáty jsou vysoké hodnoty. V nesprávném případě může dojít k ohrožení zabezpečení vaší aplikace nebo zabezpečení vašich dat.   
**Podrobnosti**: Azure Resource Manager může bezpečně nasadit certifikáty uložené v Azure Key Vault na virtuální počítače Azure při nasazení virtuálních počítačů. Když pro trezor klíčů nastavíte správné zásady přístupu, umožní vám to řídit také to, kdo získá přístup k vašemu certifikátu. Další výhodou je, že v Azure Key Vault můžete spravovat všechny certifikáty na jednom místě. Další informace najdete v tématu [nasazení certifikátů do virtuálních počítačů ze Key Vault spravovaných zákazníkem](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault) .

**Osvědčený postup**: Ujistěte se, že můžete obnovit odstranění trezorů klíčů nebo objektů trezoru klíčů.   
**Podrobnosti**: odstranění trezorů klíčů nebo objektů trezoru klíčů může být neúmyslné nebo škodlivé. V Key Vaultu povolte funkce obnovitelného odstranění a ochrany před vymazáním, zejména pro klíče používané k šifrování neaktivních uložených dat. Odstranění těchto klíčů odpovídá ztrátě dat, takže v případě potřeby můžete odstraněné trezory a objekty trezoru obnovit. Praktická cvičení Key Vaultch operací obnovení.

> [!NOTE]
> Pokud má uživatel oprávnění přispěvatele (Azure RBAC) k rovině správy trezoru klíčů, může sami udělit přístup k rovině dat nastavením zásad přístupu trezoru klíčů. Doporučujeme, abyste důkladně určili, kdo má Přispěvatel přístup k vašim trezorům klíčů, aby se zajistilo, že přístup k vašim trezorům klíčů, klíčům, tajným klíčům a certifikátům mají jenom autorizovaní uživatelé.
>
>

## <a name="manage-with-secure-workstations"></a>Správa pomocí zabezpečených pracovních stanic

> [!NOTE]
> Správce předplatného nebo vlastník by měl použít pracovní stanici zabezpečeného přístupu nebo pracovní stanici privilegovaného přístupu.
>
>

Vzhledem k tomu, že velká většina útoků cílí na koncového uživatele, se koncový bod stal jedním z primárních bodů útoku. Útočník, který nabývá tohoto koncového bodu, může pomocí přihlašovacích údajů uživatele získat přístup k datům organizace. Většina útoků na koncové body využívá skutečnost, že uživatelé jsou správci v místních pracovních stanicích.

**Osvědčený postup**: Použijte zabezpečenou pracovní stanici pro správu k ochraně citlivých účtů, úloh a dat.   
**Podrobnosti**: k omezení prostoru pro útoky v pracovních stanicích použijte [pracovní stanici privilegovaný přístup](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) . Tato zabezpečená pracovní stanice pro správu vám můžou přispět k zmírnění některých těchto útoků a zajištění bezpečnosti vašich dat.

**Osvědčený postup**: Ujistěte se, že je Endpoint Protection.   
**Podrobnosti**: vynutili zásady zabezpečení ve všech zařízeních, která se používají ke zpracování dat bez ohledu na umístění dat (v cloudu nebo v místním prostředí).

## <a name="protect-data-at-rest"></a>Ochrana neaktivních uložených dat

[Šifrování dat v klidovém umístění](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) je povinný krok k ochraně dat, dodržování předpisů a suverenity dat.

**Osvědčený postup**: použití šifrování disku k ochraně vašich dat.   
**Podrobnosti**: použijte [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md). Umožňuje správcům IT šifrovat disky virtuálních počítačů s Windows a Linux IaaS. Šifrování disku kombinuje standardní funkci Windows BitLockeru a funkci Linux dm-crypt, která poskytuje šifrování svazku pro operační systém a datové disky.

Ve výchozím nastavení se Azure Storage a Azure SQL Database šifrují data v klidovém stavu a mnoho služeb nabízí šifrování jako možnost. Ke kontrole nad klíči, které zajišťují přístup k vašim datům a jejich šifrování, můžete použít Azure Key Vault. Další informace najdete v tématu [Podpora modelu šifrování pro poskytovatele prostředků Azure](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Osvědčené postupy**: pomocí šifrování můžete zmírnit rizika související s neoprávněným přístupem k datům.   
**Podrobnosti**: Zašifrujte své jednotky předtím, než do nich zapíšete citlivá data.

Organizacím, které vynutily šifrování dat, jsou více vystaveny problémům s důvěrnými daty. Neoprávnění uživatelé můžou například ukrást data v ohrožených účtech nebo získat neoprávněný přístup k datům kódovaným v jasném formátu. Společnosti musí také prokázat, že jsou pečlivé vylaďování a používají správné bezpečnostní prvky pro zlepšení zabezpečení dat, aby byly dodrženy oborové předpisy.

## <a name="protect-data-in-transit"></a>Ochrana dat během přenosů

Ochrana dat během přenosu by měla tvořit jednu ze základních součástí vaší strategie ochrany dat. Protože data se neustále přesouvají mezi lokalitami, obecně doporučujeme při výměně dat mezi různými lokalitami vždy používat protokoly SSL/TLS. Za určitých okolností může být vhodné izolovat celý komunikační kanál mezi místní a cloudovou infrastrukturou pomocí sítě VPN.

U dat, která se přesouvají mezi vaší místní infrastrukturou a prostředím Azure, zvažte zapojení odpovídajících ochranných opatření, jako je protokol HTTPS nebo síť VPN. Při posílání šifrovaného provozu mezi virtuální sítí Azure a místním umístěním přes veřejný Internet použijte [Azure VPN Gateway](../../vpn-gateway/index.yml).

Níže jsou uvedené osvědčené postupy, které jsou specifické pro používání Azure VPN Gateway, SSL/TLS a HTTPS.

**Osvědčený postup**: zabezpečený přístup z několika pracovních stanic místně umístěných do virtuální sítě Azure.   
**Podrobnosti**: použijte [VPN typu Site-to-site](../../vpn-gateway/tutorial-site-to-site-portal.md).

**Osvědčený postup**: zabezpečený přístup z jednotlivých pracovních stanic místně umístěných do virtuální sítě Azure.   
**Podrobnosti**: použijte [VPN typu Point-to-site](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

**Osvědčený postup**: přesunutí větších datových sad pomocí vyhrazeného vysokorychlostního připojení WAN.   
**Podrobnosti**: použijte [ExpressRoute](../../expressroute/expressroute-introduction.md). Pokud se rozhodnete použít ExpressRoute, můžete posílit ochranu šifrováním dat na úrovni aplikace pomocí SSL/TLS nebo jiných protokolů.

**Osvědčený postup**: interakce s Azure Storage přes Azure Portal.   
**Podrobnosti**: všechny transakce nastávají přes HTTPS. K interakci s [Azure Storage](https://azure.microsoft.com/services/storage/)můžete použít taky [Storage REST API](/rest/api/storageservices/) přes HTTPS.

Organizace, které neumožňují ochranu dat při přenosu, jsou náchylnější k [útokům](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14))prostředníkem, [odposlouchávání](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))a zneužití relace. Tyto útoky mohou představovat první krok k získání přístupu k důvěrným datům.

## <a name="secure-email-documents-and-sensitive-data"></a>Zabezpečení e-mailu, dokumentů a citlivých dat

Chcete kontrolovat a zabezpečovat e-maily, dokumenty a citlivá data, která sdílíte mimo vaši společnost. [Azure Information Protection](/azure/information-protection/) je cloudové řešení, které organizaci pomáhá klasifikovat a chránit dokumenty a e-maily a přiřazovat k nim štítky. Můžete to udělat automaticky správci, kteří definují pravidla a podmínky, ručně podle uživatelů nebo kombinaci, kde uživatelé získávají doporučení.

Klasifikace je vždy identifikovatelná bez ohledu na to, kde jsou data uložená nebo s kým se sdílí. Popisky zahrnují vizuální označení, jako jsou například záhlaví, zápatí nebo vodoznak. Do záhlaví souborů a e-mailů se přidají metadata ve formátu prostého textu. Jasný text zajišťuje, aby další služby, jako například řešení pro ochranu před únikem informací, mohly identifikovat klasifikaci a provádět příslušné akce.

Technologie ochrany používá službu Azure Rights Management (Azure RMS). Tato technologie je integrovaná do dalších cloudových služeb a aplikací Microsoftu, jako jsou Microsoft 365 a Azure Active Directory. Tato technologie ochrany používá zásady šifrování, identity a autorizace. Ochrana, která se používá prostřednictvím Azure RMS, zůstává u dokumentů a e-mailů, nezávisle na umístění – uvnitř nebo vně vaší organizace, sítí, souborových serverů a aplikací.

Toto řešení ochrany informací vám zajišťuje kontrolu nad daty, i když je sdílená s ostatními lidmi. Můžete také použít Azure RMS s vlastními obchodními aplikacemi a řešeními ochrany informací od dodavatelů softwaru, ať už jsou aplikace a řešení místní nebo v cloudu.

Doporučený postup:

- [Nasaďte Azure Information Protection](/azure/information-protection/deployment-roadmap) pro vaši organizaci.
- Použijte popisky, které odrážejí vaše obchodní požadavky. Například: použijte popisek s názvem "vysoce důvěrné" na všechny dokumenty a e-maily, které obsahují data s horním tajným kódem, pro klasifikaci a ochranu těchto dat. K těmto datům budou mít přístup jenom autorizovaní uživatelé, a to s libovolnými omezeními, která zadáte.
- Nakonfigurujte [protokolování využití pro Azure RMS](/azure/information-protection/log-analyze-usage) , abyste mohli sledovat, jak vaše organizace používá službu ochrany.

Organizace, které mají slabý způsob [klasifikace dat](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) a ochrany souborů, můžou být náchylné k úniku dat nebo zneužití dat. Díky správné ochraně souborů můžete analyzovat toky dat a získat tak přehled o vaší firmě, detekovat rizikové chování a provádět nápravná opatření, sledovat přístup k dokumentům a tak dále.

## <a name="next-steps"></a>Další kroky

V článku [osvědčené postupy a vzory zabezpečení Azure](best-practices-and-patterns.md) najdete v tématu Doporučené postupy zabezpečení pro použití při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu pro zabezpečení Azure](/archive/blogs/azuresecurity/) – aktuální informace o nejnovější verzi v zabezpečení Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde můžou být chyby zabezpečení Microsoftu, včetně problémů s Azure, nahlášené nebo prostřednictvím e-mailu. secure@microsoft.com