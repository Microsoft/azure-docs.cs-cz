---
title: Řešení potíží s výkonem registru
description: Příznaky, příčiny a řešení běžných potíží s výkonem registru
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 15129ebe1da2e52fac106a34863f609c440549ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148405"
---
# <a name="troubleshoot-registry-performance"></a>Řešení potíží s výkonem registru

Tento článek vám pomůže řešit problémy, se kterými se můžete setkat s výkonem služby Azure Container Registry. 

## <a name="symptoms"></a>Příznaky

Může zahrnovat jednu nebo více z následujících možností:

* Vyžádané nebo nabízené obrázky s Docker CLI trvá déle, než se očekávalo.
* Nasazení imagí do služby, jako je třeba služba Azure Kubernetes, trvá déle, než se čekalo.
* V očekávaném čase nemůžete dokončit velký počet souběžných operací Pull nebo push.
* Operace vyžádání nebo Push v geograficky replikovaném registru trvá déle, než se čekalo, nebo se vložení nepovede s chybou `Error writing blob` nebo `Error writing manifest`

## <a name="causes"></a>Příčiny

* Rychlost síťového připojení může zpomalit operace v registru – [řešení](#check-expected-network-speed)
* Komprese vrstev imagí nebo extrakce může být u klienta – [řešení](#check-client-hardware) nízká.  
* Dosáhli jste nakonfigurovaného limitu úrovně služby registru nebo prostředí – [řešení](#review-configured-limits)
* Váš geograficky replikovaný registr má repliky v přilehlých oblastech – [řešení](#configure-geo-replicated-registry)
* Vyhráváte z geograficky vzdálené repliky registru – [řešení](#configure-dns-for-geo-replicated-registry)

Pokud tento problém nevyřešíte, přečtěte si téma [Pokročilé řešení potíží](#advanced-troubleshooting) a [Další kroky](#next-steps) pro další možnosti.

## <a name="potential-solutions"></a>Možná řešení

### <a name="check-expected-network-speed"></a>Kontrolovat očekávanou rychlost sítě

Zkontrolujte nahrávání a rychlost stahování v Internetu nebo použijte nástroj, jako je například AzureSpeed, k testování [nahrávání](https://www.azurespeed.com/Azure/Uploadß) a [stahování](https://www.azurespeed.com/Azure/Download) z úložiště objektů BLOB v Azure, které je hostitelem vrstev imagí registru.

Ověřte velikost obrazu s maximální podporovanou velikostí a podporovanou šířkou pásma pro stažení nebo nahrání pro vaši úroveň služby registru. Pokud je váš registr na úrovni Basic nebo Standard, zvažte možnost upgradovat, aby se zlepšil výkon. 

V případě nasazení bitové kopie do jiných služeb ověřte oblasti, ve kterých se nachází registr a cíl. Pokud chcete zvýšit výkon, zvažte nalezení registru a cíle nasazení ve stejné oblasti nebo v oblastech pro zavření sítě.

Související odkazy:

* [Azure Container Registry úrovní služeb](container-registry-skus.md)    
* [Nejčastější dotazy k registru kontejneru](container-registry-faq.md)
* [Cíle výkonu a škálovatelnosti pro Azure Blob Storage](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>Kontrolovat hardware klienta

Typ disku a procesor v klientovi Docker může ovlivnit rychlost extrakce nebo komprimace vrstev imagí v klientovi v rámci operace vyžádání nebo push. Například extrakce vrstvy na pevném disku bude trvat déle než na disku Solid-State. Porovnejte operace získání srovnatelných imagí z Azure Container registry a veřejného registru, jako je Docker Hub.

### <a name="review-configured-limits"></a>Kontrola nakonfigurovaných omezení

Pokud v registru současně přenášíte nebo načítáte více nebo mnoho imagí s více vrstvami, zkontrolujte podporovaná omezení ReadOps a WriteOps pro úroveň služby registru. Pokud je váš registr na úrovni Basic nebo Standard, zvažte možnost upgradovat, aby se limity zvýšily. Projděte si také poskytovatele sítě o omezování sítě, ke kterému může dojít při mnoha souběžných operacích. 

Zkontrolujte konfiguraci démona Docker pro maximální počet souběžných nahrávání nebo stahování pro každou operaci Push nebo Pull na klientovi. V případě potřeby nakonfigurujte vyšší omezení.

Vzhledem k tomu, že každá vrstva obrázku vyžaduje samostatnou operaci čtení nebo zápisu v registru, ověřte počet vrstev ve vašich obrázcích. Zvažte strategie pro snížení počtu vrstev imagí.

Související odkazy:

* [Azure Container Registry úrovní služeb](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Konfigurace geograficky replikovaného registru

Klient Docker, který nahraje image do geograficky replikovaného registru, nemusí do jedné replikované oblasti vložit všechny vrstvy obrázků a jejich manifest. K této situaci může dojít, protože Azure Traffic Manager směruje požadavky registru do replikovaného registru nejbližšího ze sítě. Pokud registr obsahuje dvě blízké oblasti replikace, vrstvy obrázků a manifesty, mohou být distribuovány do těchto dvou lokalit a při ověření manifestu dojde k chybě operace push.

K optimalizaci překladu DNS na nejbližší replice při vkládání imagí nakonfigurujete geograficky replikovaný registr ve stejných oblastech Azure jako zdroj nabízených operací nebo nejbližší oblast při práci mimo Azure.

K řešení potíží s geograficky replikovaným registrem můžete také dočasně zakázat směrování Traffic Manager do jedné nebo více replikací.

Související odkazy:

* [Geografická replikace v Azure Container Registry](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>Konfigurace DNS pro geograficky replikovaný registr

Pokud se operace získání dat z geograficky replikovaného registru zobrazí pomalu, konfigurace DNS na klientovi se může přeložit na geograficky vzdálený server DNS. V takovém případě může Traffic Manager směrovat požadavky do repliky, která je v síti blízko serveru DNS, ale od klienta. Spusťte nástroj, jako je například `nslookup` nebo `dig` (na platformě Linux), určete repliku, na kterou Traffic Manager směrují požadavky registru. Například:

```console
nslookup myregistry.azurecr.io
```

Potenciálním řešením je konfigurace serveru DNS s bližším využitím.

Související odkazy:

* [Geografická replikace v Azure Container Registry](container-registry-geo-replication.md)
* [Řešení potíží s nabízenými operacemi s geograficky replikovanými Registry](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [Dočasné zakázání směrování do replikace](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Nejčastější dotazy k Traffic Manager](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>Řešení potíží na pokročilé úrovni

Pokud vaše oprávnění k prostředkům registru povolí, [Projděte si stav prostředí registru](container-registry-check-health.md). Pokud dojde k chybám, přečtěte si [referenční informace o chybě](container-registry-health-error-reference.md) pro potenciální řešení.

Pokud je v registru povolená [kolekce protokolů prostředků](container-registry-diagnostics-audit-logs.md) , přečtěte si protokol ContainterRegistryRepositoryEvents. V tomto protokolu jsou uloženy informace o operacích, jako jsou akce push nebo Pull. Dotazování protokolu na [selhání operací na úrovni úložiště](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures). 

Související odkazy:

* [Protokoly pro vyhodnocení a auditování diagnostiky](container-registry-diagnostics-audit-logs.md)
* [Nejčastější dotazy k registru kontejneru](container-registry-faq.md)
* [Osvědčené postupy pro službu Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Další kroky

Pokud se vám problém nevyřeší, přečtěte si následující možnosti.

* Mezi další témata týkající se řešení potíží registru patří:
  * [Řešení potíží s přihlášením k registru](container-registry-troubleshoot-login.md)
  * [Řešení potíží se sítí pomocí registru](container-registry-troubleshoot-access.md)
* Možnosti [podpory komunity](https://azure.microsoft.com/support/community/)
* [Microsoft – otázky a odpovědi](/answers/products/)
* [Otevření lístku podpory](https://azure.microsoft.com/support/create-ticket/)