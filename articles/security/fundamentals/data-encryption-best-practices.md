---
title: Doporučené postupy zabezpečení dat a šifrování – Microsoft Azure
description: Tento článek obsahuje sadu osvědčených postupů pro zabezpečení dat a šifrování pomocí integrovaných funkcí Azure.
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
ms.openlocfilehash: c5bf62f434b2095f7200b5562c38c252a0195c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243494"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Osvědčené postupy šifrování a zabezpečení dat v Azure
Tento článek popisuje osvědčené postupy pro zabezpečení dat a šifrování.

Osvědčené postupy jsou založeny na konsensu názoru a pracují s aktuálními funkcemi platformy Azure a sadami funkcí. Názory a technologie se v průběhu času mění a tento článek je pravidelně aktualizován, aby tyto změny odrážel.

## <a name="protect-data"></a>Ochrana dat
Chcete-li chránit data v cloudu, je třeba účet pro možné stavy, ve kterých může dojít k datům a jaké ovládací prvky jsou k dispozici pro tento stav. Doporučené postupy pro zabezpečení a šifrování dat Azure se týkají následujících stavů dat:

- V klidovém stavu: To zahrnuje všechny objekty úložiště informací, kontejnery a typy, které existují staticky na fyzickém médiu, ať už magnetický nebo optický disk.
- Přenos: Při přenosu dat mezi součástmi, lokacemi nebo programy jsou data na cestě. Příklady jsou přenos přes síť, přes sběrnici (z místní do cloudu a naopak, včetně hybridních připojení, jako je ExpressRoute) nebo během procesu vstupu a výstupu.

## <a name="choose-a-key-management-solution"></a>Vyberte si klíčové řešení pro správu

Ochrana klíčů je nezbytná pro ochranu dat v cloudu.

[Azure Key Vault](/azure/key-vault/key-vault-overview) pomáhá chránit kryptografické klíče a tajné kódy používané cloudovými aplikacemi a službami. Key Vault zjednodušuje proces správy klíčů a zajišťuje vám kontrolu nad klíči, které se používají k přístupu a šifrování dat. Vývojáři můžou během pár minut vytvořit klíče pro vývoj a testování a potom je migrovat na produkční klíče. Správci zabezpečení můžou klíčům podle potřeby udělovat (a odvolávat) oprávnění.

Trezor klíčů můžete použít k vytvoření více zabezpečených kontejnerů nazývaných trezory. Tyto trezory jsou podporovány pomocí hsm. Trezory pomáhají snížit riziko nechtěné ztráty informací o zabezpečení tím, že centralizují ukládání tajných klíčů aplikací. Trezory klíčů také řídí a protokolují přístup ke všem položkám, které jsou v nich uložené. Azure Key Vault může zpracovávat požadavky a obnovení certifikátů tls (Transport Layer Security). Poskytuje funkce pro robustní řešení pro správu životního cyklu certifikátů.

Azure Key Vault je navržen tak, aby podporoval klíče aplikace a tajné klíče. Trezor klíčů není určen jako úložiště uživatelských hesel.

Níže jsou uvedeny doporučené postupy zabezpečení pro použití trezoru klíčů.

**Osvědčený postup**: Udělte přístup uživatelům, skupinám a aplikacím v určitém oboru.   
**Podrobnosti**: Použijte předdefinované role RBAC. Chcete-li například udělit uživateli přístup ke správě trezorů klíčů, přiřadíte tomuto uživateli předdefinovaný [přispěvatel role Key Vault v](/azure/role-based-access-control/built-in-roles) určitém oboru. Obor v tomto případě by odběr, skupina prostředků nebo pouze konkrétní trezor klíčů. Pokud předdefinované role neodpovídají vašim potřebám, můžete [definovat vlastní role](/azure/role-based-access-control/custom-roles).

**Osvědčený postup**: Řízení toho, k čemu mají uživatelé přístup.   
**Detail**: Přístup k trezoru klíčů je řízen prostřednictvím dvou samostatných rozhraní: roviny správy a roviny dat. Řízení přístupu roviny správy a roviny dat fungují nezávisle.

Pro řízení přístupu uživatelů používejte RBAC. Například pokud chcete udělit aplikaci přístup k použití klíčů v trezoru klíčů, stačí udělit přístupová oprávnění rovina data pomocí zásad přístupu trezoru klíčů a žádný přístup k rovině správy je potřeba pro tuto aplikaci. A obráceně, pokud chcete, aby uživatel mohl číst vlastnosti a značky trezoru, ale neměl přístup k žádným klíčům, tajným kódům ani certifikátům, můžete tomuto uživateli udělit přístup pro čtení pomocí RBAC a není potřeba žádný přístup k rovině dat.

**Osvědčený postup:** Ukládejte certifikáty do trezoru klíčů. Vaše certifikáty mají vysokou hodnotu. Ve špatných rukou může být ohrožena bezpečnost vaší aplikace nebo zabezpečení vašich dat.   
**Podrobnosti:** Azure Resource Manager můžete bezpečně nasadit certifikáty uložené v Azure Key Vault do virtuálních počítačů Azure při nasazení virtuálních počítačů. Když pro trezor klíčů nastavíte správné zásady přístupu, umožní vám to řídit také to, kdo získá přístup k vašemu certifikátu. Další výhodou je, že v Azure Key Vault můžete spravovat všechny certifikáty na jednom místě. Další informace [najdete v tématu Nasazení certifikátů do virtuálních aplikací z trezoru klíčů spravovaného zákazníkem.](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)

**Osvědčený postup:** Ujistěte se, že můžete obnovit odstranění trezorů klíčů nebo objektů trezoru klíčů.   
**Detail**: Odstranění trezorů klíčů nebo objektů trezoru klíčů může být neúmyslné nebo škodlivé. V Key Vaultu povolte funkce obnovitelného odstranění a ochrany před vymazáním, zejména pro klíče používané k šifrování neaktivních uložených dat. Odstranění těchto klíčů odpovídá ztrátě dat, takže v případě potřeby můžete odstraněné trezory a objekty trezoru obnovit. Pravidelně procvičujte operace obnovení trezoru klíčů.

> [!NOTE]
> Pokud má uživatel oprávnění role Přispěvatel (RBAC) k rovině správy trezoru klíčů, může sám sobě udělit i přístup k rovině dat nastavením zásad přístupu trezoru klíčů. Doporučujeme, abyste měli pevně kontrolu nad tím, kdo má přístup přispěvatele k trezorům klíčů, abyste měli k vašim trezorům klíčů, klíčům, tajným klíčům a certifikátům přístup a správu pouze oprávněných osob.
>
>

## <a name="manage-with-secure-workstations"></a>Správa pomocí zabezpečených pracovních stanic

> [!NOTE]
> Správce nebo vlastník předplatného by měl používat pracovní stanici zabezpečeného přístupu nebo pracovní stanici s privilegovaným přístupem.
>
>

Vzhledem k tomu, že drtivá většina útoků cílí na koncového uživatele, koncový bod se stane jedním z primárních bodů útoku. Útočník, který ohrožuje koncový bod, může pomocí pověření uživatele získat přístup k datům organizace. Většina útoků koncového bodu využít skutečnost, že uživatelé jsou správci ve svých místních pracovních stanic.

**Osvědčený postup:** K ochraně citlivých účtů, úkolů a dat použijte zabezpečenou pracovní stanici pro správu.   
**Podrobnosti**: Pomocí [pracovní stanice s privilegovaným přístupem](https://technet.microsoft.com/library/mt634654.aspx) zmenšete prostor pro útok na pracovních stanicích. Tyto pracovní stanice pro bezpečnou správu vám mohou pomoci zmírnit některé z těchto útoků a zajistit, aby vaše data byla bezpečnější.

**Osvědčený postup**: Zajistěte ochranu koncového bodu.   
**Podrobnosti**: Vynucujte zásady zabezpečení napříč všemi zařízeními, která se používají ke spotřebování dat, bez ohledu na umístění dat (cloud nebo místní).

## <a name="protect-data-at-rest"></a>Ochrana neaktivních uložených dat

[Šifrování dat v klidovém stavu](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) je povinným krokem k ochraně osobních údajů, dodržování předpisů a suverenitě dat.

**Osvědčený postup:** Použijte šifrování disku, které pomáhá chránit data.   
**Detail**: Použijte [šifrování disku Azure](/azure/security/azure-security-disk-encryption-overview). Umožňuje správcům IT šifrovat disky virtuálního počítače Windows a Linux IaaS. Šifrování disku kombinuje standardní funkci nástroje Windows BitLocker a funkci Linux dm-crypt a poskytuje šifrování svazku pro operační systém a datové disky.

Azure Storage a Azure SQL Database šifrují data v klidovém stavu ve výchozím nastavení a mnoho služeb nabízí šifrování jako možnost. Ke kontrole nad klíči, které zajišťují přístup k vašim datům a jejich šifrování, můžete použít Azure Key Vault. Další [informace najdete v tématu Podpora modelu šifrování poskytovatelů prostředků Azure.](encryption-atrest.md#azure-resource-providers-encryption-model-support)

**Doporučené postupy**: Pomocí šifrování můžete zmírnit rizika spojená s neoprávněným přístupem k datům.   
**Detail**: Před zápisem citlivých dat do nich zašifrujte jednotky.

Organizace, které nevynucují šifrování dat, jsou více vystaveny problémům s důvěrností dat. Neoprávnění nebo nepoctiví uživatelé mohou například ukrást data v ohrožených účtech nebo získat neoprávněný přístup k datům kódovaným v formátu Clear Format. Společnosti musí také prokázat, že jsou pečlivé a používají správné bezpečnostní kontroly ke zvýšení bezpečnosti svých údajů, aby byly v souladu s průmyslovými předpisy.

## <a name="protect-data-in-transit"></a>Ochrana dat během přenosů

Ochrana dat během přenosu by měla tvořit jednu ze základních součástí vaší strategie ochrany dat. Protože data se neustále přesouvají mezi lokalitami, obecně doporučujeme při výměně dat mezi různými lokalitami vždy používat protokoly SSL/TLS. Za určitých okolností může být vhodné izolovat celý komunikační kanál mezi místní a cloudovou infrastrukturou pomocí sítě VPN.

U dat, která se přesouvají mezi vaší místní infrastrukturou a prostředím Azure, zvažte zapojení odpovídajících ochranných opatření, jako je protokol HTTPS nebo síť VPN. Při odesílání šifrovaného provozu mezi virtuální sítí Azure a místním umístěním přes veřejný internet použijte [Azure VPN Gateway](../../vpn-gateway/index.yml).

Následují doporučené postupy specifické pro používání Azure VPN Gateway, SSL/TLS a HTTPS.

**Osvědčený postup:** Zabezpečený přístup z více pracovních stanic umístěných místně do virtuální sítě Azure.   
**Detail**: Použijte [síť VPN site-to-site](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Osvědčený postup:** Zabezpečený přístup z jednotlivých pracovních stanic umístěných místně do virtuální sítě Azure.   
**Detail**: Použití [vpn z bodu na místo](/azure/vpn-gateway/vpn-gateway-point-to-site-create).

**Osvědčený postup:** Přesunutí větších datových sad přes vyhrazené vysokorychlostní připojení WAN.   
**Detail**: Použijte [ExpressRoute](/azure/expressroute/expressroute-introduction). Pokud se rozhodnete použít ExpressRoute, můžete posílit ochranu šifrováním dat na úrovni aplikace pomocí SSL/TLS nebo jiných protokolů.

**Osvědčený postup:** Interakce s Azure Storage prostřednictvím portálu Azure.   
**Detail**: Všechny transakce probíhají prostřednictvím protokolu HTTPS. Rozhraní API [STORAGE REST over](https://msdn.microsoft.com/library/azure/dd179355.aspx) HTTPS můžete také použít k interakci s [Azure Storage](https://azure.microsoft.com/services/storage/).

Organizace, které nedokáží chránit data při přenosu, jsou náchylnější k [útokům man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [odposlouchávání](https://technet.microsoft.com/library/gg195641.aspx)a únosu relace. Tyto útoky mohou představovat první krok k získání přístupu k důvěrným datům.

## <a name="secure-email-documents-and-sensitive-data"></a>Zabezpečení e-mailů, dokumentů a citlivých dat

Chcete ovládat a zabezpečit e-maily, dokumenty a citlivá data, která sdílíte mimo vaši společnost. [Azure Information Protection](/azure/information-protection/) je cloudové řešení, které organizaci pomáhá klasifikovat a chránit dokumenty a e-maily a přiřazovat k nim štítky. To může být provedeno automaticky správci, kteří definují pravidla a podmínky, ručně uživateli nebo kombinací, kde uživatelé získají doporučení.

Klasifikace je identifikovatelná za všech okolností bez ohledu na to, kde jsou data uložena nebo s kým jsou sdílena. Popisky zahrnují vizuální označení, jako jsou například záhlaví, zápatí nebo vodoznak. Do záhlaví souborů a e-mailů se přidají metadata ve formátu prostého textu. Prosté znění zajišťuje, že ostatní služby, jako jsou například řešení, která zabrání ztrátě dat, mohou identifikovat klasifikaci a přijmout vhodná opatření.

Technologie ochrany používá Azure Rights Management (Azure RMS). Tato technologie se integruje s dalšími cloudovými službami a aplikacemi Microsoftu, například s Office 365 nebo Azure Active Directory. Tato technologie ochrany používá zásady šifrování, identity a autorizace. Ochrana, která se používá prostřednictvím Služby Azure RMS, zůstává s dokumenty a e-maily, nezávisle na umístění – uvnitř nebo vně vaší organizace, sítí, souborových serverů a aplikací.

Toto řešení ochrany informací vám udržuje kontrolu nad vašimi daty, i když jsou sdílena s jinými lidmi. Azure RMS můžete také používat s vlastními obchodními aplikacemi a řešeními ochrany informací od dodavatelů softwaru, ať už jsou tyto aplikace a řešení místní nebo v cloudu.

Doporučený postup:

- [Nasaďte Azure Information Protection](/azure/information-protection/deployment-roadmap) pro vaši organizaci.
- Použijte štítky, které odrážejí vaše obchodní požadavky. Příklad: Použijte popisek s názvem "vysoce důvěrné" pro všechny dokumenty a e-maily, které obsahují přísně tajná data, klasifikovat a chránit tato data. Potom k těmto datům mají přístup pouze oprávnění uživatelé se všemi zadanými omezeními.
- Nakonfigurujte [protokolování využití pro Azure RMS,](/azure/information-protection/log-analyze-usage) abyste mohli sledovat, jak vaše organizace používá službu ochrany.

Organizace, které jsou slabé v [klasifikaci dat](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) a ochraně souborů, mohou být náchylnější k úniku dat nebo zneužití dat. Díky správné ochraně souborů můžete analyzovat toky dat, abyste získali přehled o vaší firmě, zjistili rizikové chování a přijali nápravná opatření, sledovali přístup k dokumentům a tak dále.

## <a name="next-steps"></a>Další kroky

Podívejte se na [osvědčené postupy a vzory zabezpečení Azure, kde](best-practices-and-patterns.md) najdete další doporučené postupy zabezpečení, které se používají při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovějších informacích o azure securityu
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde lze nahlásit chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo e-mailemsecure@microsoft.com
