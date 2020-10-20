---
title: Co je nového pro Azure Key Vault
description: Nedávné aktualizace pro Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: fac5fad51137cd08f2498db132768263a770430d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203870"
---
# <a name="whats-new-for-azure-key-vault"></a>Co je nového pro Azure Key Vault

Tady je co je nový s Azure Key Vault. Nové funkce a vylepšení jsou také oznamovány na [Key Vaultm kanálu služby Azure Updates](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="july-2020"></a>Červenec 2020

> [!WARNING]
> Tyto dvě aktualizace mají schopnost ovlivnit Azure Key Vault implementace.

### <a name="soft-delete-on-by-default"></a>Obnovitelné odstranění ve výchozím nastavení

Po konci 2020 **budou obnovitelné odstranění standardně zapnuté pro všechny trezory klíčů**, a to jak nové, tak i již existující. Úplné podrobnosti o této potenciálně zásadní změně a postupu při hledání ovlivněných trezorů klíčů a jejich opětovné aktualizace najdete v článku [tiché odstranění bude povoleno u všech trezorů klíčů](soft-delete-change.md). 

### <a name="azure-tls-certificate-changes"></a>Změny certifikátu Azure TLS  

Microsoft aktualizuje služby Azure tak, aby používaly certifikáty TLS z jiné sady kořenových certifikačních autorit (CAs). Tato změna se provádí, protože aktuální certifikáty certifikační autority [nejsou v rozporu s jedním z požadavků na základní hodnoty fóra CA nebo prohlížeče](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951).

### <a name="when-will-this-change-happen"></a>Kdy bude tato změna provedena?

- Služba [Azure Active Directory](/azure/active-directory) (Azure AD) zahájila tento přechod 7. července 2020.
- Všechny nově vytvořené koncové body služby Azure TLS/SSL obsahují aktualizované certifikáty, které jsou zřetězené až s novými kořenovými certifikačními autoritami.
- Stávající koncové body Azure se budou postupně přecházet ve fázi od 13. srpna 2020.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) a [DPS](/azure/iot-dps/) zůstanou v kořenové certifikační autoritě Baltimore CyberTrust, ale jejich zprostředkující certifikační autority se změní. Úplné podrobnosti najdete v tématu objekt BLOB po [Azure IoT TLS: změny přicházejí! (... a proč vám záleží)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- [Azure Storage](/azure/storage) zůstane v kořenové certifikační autoritě Baltimore CyberTrust, ale jejich zprostředkující certifikační autority se změní. Úplné podrobnosti najdete v tématu objekt BLOB post [Azure Storage TLS: změny přicházejí! (... a proč vám záleží)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).

> [!IMPORTANT]
> Zákazníci možná budou muset po této změně aktualizovat své aplikace, aby se předešlo chybám připojení při pokusu o připojení ke službám Azure.

### <a name="what-is-changing"></a>Co se mění?

V dnešní době je většina certifikátů TLS používaných službami Azure zřetězená s následující kořenovou certifikační autoritou:

| Běžný název certifikační autority | Kryptografický otisk (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Certifikáty TLS používané službami Azure budou zřetězit až do jednoho z následujících kořenových certifikačních autorit:

| Běžný název certifikační autority | Kryptografický otisk (SHA1) |
|--|--|
| [Globální kořenový adresář G2 DigiCert](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [Globální kořenová certifikační autorita DigiCert](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D – DŮVĚRYHODNÁ kořenová třída 3, CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Kořenová certifikační autorita RSA společnosti Microsoft 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Kořenová certifikační autorita Microsoft EV ECC 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Kdy je možné vyřadit starý zprostředkující kryptografický otisk?

Aktuální certifikáty certifikační autority *nebudou* odvolány do 15. února 2021. Po tomto datu můžete ze svého kódu odebrat staré kryptografické otisky.

Pokud se změní toto datum, budete upozorněni na nové datum odvolání.

### <a name="will-this-change-affect-me"></a>Bude se tato změna týkat? 

Očekáváme, že **většina zákazníků Azure** nebude mít vliv na.  Vaše aplikace ale může mít vliv, pokud explicitně určí seznam přijatelných certifikačních autorit. Tento postup se označuje jako připnutí certifikátu.

Tady je několik způsobů, jak zjistit, jestli je vaše aplikace ovlivněná:

- Vyhledejte ve zdrojovém kódu svůj kryptografický otisk, běžný název a další vlastnosti certifikátu pro všechny certifikační autority IT společnosti Microsoft, které najdete [tady](https://www.microsoft.com/pki/mscorp/cps/default.htm). Pokud dojde ke shodě, bude ovlivněna vaše aplikace. Pokud chcete tento problém vyřešit, aktualizujte zdrojový kód tak, aby obsahoval nové certifikační autority. V rámci osvědčeného postupu se ujistěte, že je možné certifikační autority přidat nebo upravit v krátkém časovém oznámení. Oborové předpisy vyžadují, aby se certifikáty certifikační autority nahradily během sedmi dnů, takže se zákazníci, kteří spoléhají na připnutí, musí reagovat rychle.

- Pokud máte aplikaci, která se integruje s rozhraními API Azure nebo jinými službami Azure, a nejste si jistí, jestli používá připnutí certifikátů, obraťte se na dodavatele aplikace.

- Různé operační systémy a jazykové moduly runtime, které komunikují se službami Azure, můžou vyžadovat další kroky pro správné sestavení řetězce certifikátů s těmito novými kořeny:
    - **Linux**: řada distribucí vyžaduje, abyste do/etc/SSL/certs. přidali certifikační autority. Konkrétní pokyny najdete v dokumentaci k distribuci.
    - **Java**: Ujistěte se, že úložiště klíčů Java obsahuje výše uvedené CAS.
    - **Windows spuštěné v odpojených prostředích**: systémy běžící v odpojených prostředích budou muset mít do úložiště důvěryhodných kořenových certifikačních autorit přidané nové kořeny a do úložiště zprostředkujících certifikačních autorit se přidaly zprostředkující.
    - **Android**: Projděte si dokumentaci k vašemu zařízení a verzi Androidu.
    - **Další hardwarová zařízení, zejména IoT**: obraťte se na výrobce zařízení.

- Pokud máte prostředí, ve kterém jsou pravidla brány firewall nastavená tak, aby umožňovala odchozí volání jenom na určitý seznam odvolaných certifikátů (CRL) stáhnout a/nebo online ověřovací umístění protokolu OCSP (Online Certificate Status Protocol). Bude potřeba, abyste povolili následující seznam CRL a adresy URL protokolu OCSP:

    - http://crl3&#46;d igicert&#46;com
    - http://crl4&#46;d igicert&#46;com
    - http://ocsp&#46;d igicert&#46;com
    - http://www&#46;d-Trust&#46;NET
    - http://root-c3-ca2-2009&#46;&#46;protokolu OCSP-Trust&#46;NET
    - http://crl&#46; Microsoft&#46;com
    - http://oneocsp&#46; Microsoft&#46;com
    - http://ocsp&#46; msocsp&#46;com

## <a name="june-2020"></a>Červen 2020

Azure Monitor pro Key Vault je nyní ve verzi Preview.  Azure Monitor poskytuje ucelený pohled na vaše trezory klíčů tím, že nabízí jednotný přehled o vašich Key Vaultch požadavcích, výkonu, selháních a latenci. Další informace najdete v tématu [Azure monitor pro Key Vault (Preview).](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="may-2020"></a>Květen 2020

Key Vault "Přineste si vlastní klíč" (BYOK) je teď všeobecně dostupná. Přečtěte si část [Azure Key Vault BYOK Specification](../keys/byok-specification.md)a Naučte se [importovat klíče chráněné HSM do Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Březen 2020

Soukromé koncové body jsou nyní k dispozici ve verzi Preview. Služba privátního propojení Azure umožňuje přístup k Azure Key Vault a hostovaným zákaznickým a partnerským službám Azure přes privátní koncový bod ve vaší virtuální síti.  Naučte se [integrovat Key Vault s privátním propojením Azure](private-link-service.md).

## <a name="2019"></a>2019

- Vydání Azure Key Vault sad SDK nové generace. Příklady jejich použití najdete v tématu rychlý Start Azure Key Vault tajných kódů pro [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)a [Node.js](../secrets/quick-create-node.md)
- Nové zásady Azure pro správu certifikátů trezoru klíčů. Přečtěte si [Azure Policy předdefinované definice pro Key Vault](../policy-samples.md).
- Rozšíření virtuálního počítače Azure Key Vault je teď všeobecně dostupné.  Viz [Key Vault rozšíření pro virtuální počítače pro Linux](../../virtual-machines/extensions/key-vault-linux.md) a [rozšíření virtuálního počítače Key Vault pro Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Správa tajných kódů řízených událostmi pro Azure Key Vault nyní k dispozici v Azure Event Grid. Další informace najdete v tématu [schéma Event Grid pro události v Azure Key Vault] (.. /.. /Event-Grid/Event-Schema-Key-Vault.MD] a Naučte se, jak [přijímat a reagovat na oznámení o trezoru klíčů pomocí Azure Event Grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nové funkce a integrace vydané tento rok:

- Integrace s Azure Functions. Příklad scénáře využití [Azure Functions](../../azure-functions/index.yml) pro operace trezoru klíčů najdete v tématu [Automatizace rotace tajného](../secrets/tutorial-rotation.md)klíče. 
- [Integrace s Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Díky tomu Azure Databricks nyní podporuje dva typy tajných oborů: zálohovaných Azure Key Vault a datacihly. Další informace najdete v tématu [Vytvoření oboru tajného klíče, který je Azure Key Vault zálohovaný](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope) .
- [Koncové body služby virtuální sítě pro Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nové funkce vydané tento rok:

- Spravované klíče účtu úložiště. Funkce klíčů účtu úložiště přidala snazší integraci s Azure Storage. Další informace najdete v tématu Přehled – [Přehled klíčů spravovaného účtu úložiště](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Obnovitelné odstranění. Funkce obnovitelného odstranění vylepšuje ochranu dat vašich trezorů klíčů a objektů trezoru klíčů. Další informace najdete v tématu Přehled – [Přehled o tichém odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Nové funkce vydané tento rok:
- Správa certifikátů: Přidáno do 26. září 2016 jako funkce pro GA verze 2015-06-01.

Obecná dostupnost (verze 2015-06-01) byla oznámena 24. června 2015. V této verzi byly provedeny následující změny: 
- Odstranění klíče – pole použít se odebralo.
- Načte informace o klíči – pole použít se odebralo.
- Import klíče do trezoru – pole použít se odebralo.
- Obnoví klíč – pole použít se odebralo.     
- Byl změněn "RSA_OAEP" na "RSA-výplně OAEP" pro algoritmy RSA. Přečtěte si [o klíčích, tajných klíčích a certifikátech](about-keys-secrets-certificates.md).    
 
Verze Second Preview (verze 2015-02-01-Preview) byla oznámena 20. dubna 2015. Další informace najdete v příspěvku na blogu [REST API Update](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) . Následující úkoly byly aktualizovány:
 
- Výpis klíčů v trezoru – Podpora stránkování je přidána do provozu.
- Vypíše verze operace přidané klíčovým seznamem verzí klíče.  
- Výpis tajných klíčů v trezoru – Podpora stránkování byla přidána.
- Vypíše verze operace s tajným kódem, aby vypisovat verze tajného kódu.  
- Všechny operace – do atributů se přidaly vytvořená a aktualizovaná časová razítka.  
- Vytvoření tajného klíče přidávaného obsahu k tajným klíčům.
- Vytvořte značky přidané klíčovým slovem jako volitelné informace.
- Vytvoření značek přidaných po tajnosti jako volitelných informací.
- Aktualizace značek přidaných klíčovým slovem jako volitelné informace
- Aktualizace značek přidaných tajnými znaky jako volitelné informace
- Změnila se maximální velikost tajných kódů z 10 K na 25 kB. Přečtěte si informace [o klíčích, tajných klíčích a certifikátech](about-keys-secrets-certificates.md).    

## <a name="2014"></a>2014
 
První verze Preview (verze 2014-12-08-Preview) byla oznámena 8. ledna 2015.  

## <a name="next-steps"></a>Další kroky

Pokud máte další otázky, kontaktujte nás prosím prostřednictvím [podpory](https://azure.microsoft.com/support/options/).  
