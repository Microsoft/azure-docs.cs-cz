---
title: Modely šifrování dat v Microsoft Azure
description: Tento článek poskytuje přehled modelů šifrování dat v Microsoft Azure.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: 836e01d3cd8fb25dda1616803d8b6f3e9ff4e06f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89645747"
---
# <a name="data-encryption-models"></a>Modely šifrování dat

Porozumění různým modelům šifrování a jejich specialistům a nevýhodám je nezbytné pro porozumění způsobu, jakým různé poskytovatele prostředků v Azure implementují šifrování v klidovém formátu. Tyto definice se sdílejí napříč všemi poskytovateli prostředků v Azure, aby se zajistil společný jazyk a taxonomie.

Pro šifrování na straně serveru existují tři scénáře:

- Šifrování na straně serveru pomocí Service-Managedch klíčů
  - Poskytovatelé prostředků Azure provádějí operace šifrování a dešifrování.
  - Microsoft spravuje klíče
  - Plná funkčnost cloudu

- Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v Azure Key Vault
  - Poskytovatelé prostředků Azure provádějí operace šifrování a dešifrování.
  - Klíče řízení zákazníka prostřednictvím Azure Key Vault
  - Plná funkčnost cloudu

- Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem na hardwaru ovládaném zákazníkem
  - Poskytovatelé prostředků Azure provádějí operace šifrování a dešifrování.
  - Klávesy řízení zákazníka na hardwaru ovládaném zákazníkem
  - Plná funkčnost cloudu

Modely šifrování na straně serveru odkazují na šifrování, které provádí služba Azure. V tomto modelu poskytovatel prostředků provádí operace šifrování a dešifrování. Azure Storage například může přijímat data v operacích s prostým textem a provede šifrování a dešifrování interně. Poskytovatel prostředků může používat šifrovací klíče spravované společností Microsoft nebo zákazníkem v závislosti na zadané konfiguraci.

![Server](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Každé šifrování na straně serveru v modelech REST zahrnuje různé charakteristiky správy klíčů. To zahrnuje místo, kde a jak se vytvářejí šifrovací klíče a ukládají se i modely přístupu a postupy střídání klíčů.  

Pro šifrování na straně klienta Vezměte v úvahu následující skutečnosti:

- Služby Azure nevidí dešifrovaná data
- Zákazníci spravují a ukládají klíče místně (nebo v jiných zabezpečených úložištích). Pro služby Azure nejsou klíče k dispozici.
- Omezené funkce cloudu

Podporované modely šifrování v Azure se rozdělí do dvou hlavních skupin: "šifrování klienta" a "šifrování na straně serveru", jak je uvedeno výše. Nezávisle na šifrování používaného modelu REST služby Azure vždycky doporučují používat zabezpečený přenos, jako je TLS nebo HTTPS. Šifrování v přenosu by proto mělo být adresováno transportním protokolem a nemělo by být hlavním faktorem při určování šifrování používaného modelu REST.

## <a name="client-encryption-model"></a>Model šifrování klienta

Model šifrování klienta odkazuje na šifrování, které se provádí mimo poskytovatele prostředků nebo Azure pomocí služby nebo volání aplikace. Šifrování může provádět aplikace služby v Azure nebo aplikace spuštěná v zákaznickém datovém centru. Pokud se v obou případech využívá tento model šifrování, poskytovatel prostředků Azure obdrží zašifrovaný objekt BLOB dat bez možnosti dešifrovat data jakýmkoli způsobem nebo mít přístup k šifrovacím klíčům. V tomto modelu se Správa klíčů provádí pomocí volající služby nebo aplikace a je neprůhledná pro službu Azure.

![Klient](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Šifrování na straně serveru pomocí klíčů spravovaných službou

U mnoha zákazníků je základním požadavkem zajistit, aby data byla zašifrována, kdykoli jsou v klidovém stavu. Šifrování na straně serveru pomocí klíčů spravovaných službou umožňuje tomuto modelu povolit zákazníkům označit konkrétní prostředek (účet úložiště, databázi SQL atd.) pro šifrování a ponechat všechny aspekty správy klíčů, jako je vystavování klíčů, rotace a zálohování do Microsoftu. Většina služeb Azure, které podporují šifrování v klidovém umístění, obvykle podporuje tento model přesměrování správy šifrovacích klíčů do Azure. Poskytovatel prostředků Azure vytvoří klíče, umístí je do zabezpečeného úložiště a v případě potřeby je načte. To znamená, že služba má úplný přístup ke klíčům a služba má plnou kontrolu nad správou životního cyklu přihlašovacích údajů.

![starosti](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

Šifrování na straně serveru pomocí klíčů spravovaných službou proto rychle řeší nutnost šifrování v klidovém provozu s nízkou režií zákazníkovi. Pokud je k dispozici zákazník, obvykle otevře Azure Portal pro cílového předplatného a poskytovatele prostředků a zkontroluje box s označením, že chcete data šifrovat. V některých správců prostředků je šifrování na straně serveru s klíči spravovanými službami ve výchozím nastavení zapnuté.

Šifrování na straně serveru pomocí klíčů spravovaných Microsoftem předpokládá, že služba má úplný přístup k ukládání a správě klíčů. I když někteří zákazníci můžou chtít klíče spravovat, protože mají větší zabezpečení, měli byste při vyhodnocování tohoto modelu vzít v úvahu náklady a rizika spojené s vlastním řešením úložiště klíčů. V mnoha případech může organizace určit, že omezení prostředků nebo rizika místního řešení můžou být větší než riziko správy cloudu šifrování v klávesách REST.  Tento model však nemusí být dostačující pro organizace, které mají požadavky na řízení vytváření nebo životního cyklu šifrovacích klíčů nebo mají různé pracovníky spravovat šifrovací klíče služby, než jsou ta, která spravuje službu (to znamená oddělení správy klíčů od celkového modelu správy pro danou službu).

### <a name="key-access"></a>Přístup ke klíči

Pokud se používá šifrování na straně serveru pomocí klíčů spravovaných službou, je vytvoření klíče, úložiště a přístup ke službě spravováno službou. Obecně platí, že základní poskytovatelé prostředků Azure budou ukládat šifrovací klíče dat do úložiště, které je blízko dat a budou rychle dostupné a přístupné, když jsou šifrovací klíče uložené v zabezpečeném interním úložišti.

**Výhody**

- Jednoduché nastavení
- Microsoft spravuje střídání klíčů, zálohování a redundanci.
- Zákazník nemá náklady spojené s implementací nebo rizikem vlastního schématu správy klíčů.

**Nevýhody**

- Žádné řízení od zákazníka u šifrovacích klíčů (specifikace klíče, životní cyklus, odvolání atd.)
- Není možné oddělit správu klíčů od celkového modelu správy pro službu.

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v Azure Key Vault

U scénářů, kde požadavek slouží k šifrování uložených dat a řízení uživatelů šifrovacích klíčů, můžou používat šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v Key Vault. Některé služby můžou ukládat jenom šifrovací klíč kořenového klíče v Azure Key Vault a uložit šifrovaný šifrovací klíč dat do interního umístění blíže k datům. V takovém scénáři můžou zákazníci přinášet vlastní klíče Key Vault (BYOK – Bring Your Own Key) nebo generovat nové a použít je k zašifrování požadovaných prostředků. I když poskytovatel prostředků provádí operace šifrování a dešifrování, používá nakonfigurovaný klíč šifrování klíče jako kořenový klíč pro všechny operace šifrování.

Ztráta klíčového šifrovacího klíče znamená ztrátu dat. Z tohoto důvodu by klíče neměly být odstraněny. Klíče by se měly zálohovat vždy, když se vytvoří nebo otočí. [Obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) by mělo být povolené v jakémkoli trezoru, který ukládá šifrovací klíče klíčů. Místo odstranění klíče nastavte možnost povoleno na hodnotu NEPRAVDA nebo nastavte datum vypršení platnosti.

### <a name="key-access"></a>Přístup ke klíči

Model šifrování na straně serveru s klíčem spravovaným zákazníkem v Azure Key Vault zahrnuje službu, která při přístupu k klíčům zašifruje a dešifruje podle potřeby. Šifrování v klíčích REST zpřístupňuje služba prostřednictvím zásad řízení přístupu. Tato zásada uděluje přístup k identitě služby pro získání klíče. Služba Azure spuštěná v rámci přidruženého předplatného se dá nakonfigurovat s identitou v tomto předplatném. Služba může provést Azure Active Directory ověřování a získat ověřovací token, který identifikuje sám sebe jako tuto službu jednající jménem předplatného. Pomocí tohoto tokenu je pak možné Key Vault získat klíč, kterému byl udělen přístup k.

U operací využívajících šifrovací klíče může být identitě služby udělen přístup k některým z následujících postupů: dešifrovat, šifrovat, unwrapKey, wrapKey, ověřit, podepsat, získat, vypsat, aktualizovat, vytvořit, importovat, odstranit, zálohovat a obnovit.

Chcete-li získat klíč pro použití při šifrování nebo dešifrování dat v případě, že je spuštěna instance služby Správce prostředků, musí mít UnwrapKey (k získání klíče pro dešifrování) a WrapKey (pro vložení klíče do trezoru klíčů při vytváření nového klíče).

>[!NOTE]
>Další podrobnosti o autorizaci Key Vault najdete na stránce zabezpečení trezoru klíčů v [dokumentaci k Azure Key Vault](../../key-vault/general/secure-your-key-vault.md).

**Výhody**

- Úplná kontrola nad použitými klíči – šifrovací klíče se spravují v Key Vault zákazníka pod řízením zákazníka.
- Možnost šifrovat více služeb do jedné hlavní větve
- Může oddělit správu klíčů od celkového modelu správy pro službu.
- Může definovat službu a umístění klíčů v různých oblastech.

**Nevýhody**

- Zákazník má plnou zodpovědnost za správu přístupu ke klíčům.
- Zákazník má plnou zodpovědnost za správu životního cyklu klíčů.
- Další nastavení & režijních nákladů na konfiguraci

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v hardwaru ovládaném zákazníkem

Některé služby Azure umožňují model správy klíčů hostitele vlastní klíč (HYOK). Tento režim správy je užitečný ve scénářích, kdy je potřeba zašifrovat neaktivní data a spravovat klíče ve speciálním úložišti mimo kontrolu Microsoftu. V tomto modelu musí služba získat klíč z externího webu. Jsou ovlivněny záruky výkonu a dostupnosti a konfigurace je složitější. Vzhledem k tomu, že služba má přístup k klíč DEK během operace šifrování a dešifrování, jsou celkové záruky zabezpečení tohoto modelu podobné tomu, když jsou klíče spravované zákazníkem v Azure Key Vault.  V důsledku toho tento model není vhodný pro většinu organizací, pokud nemá specifické požadavky na správu klíčů. V důsledku těchto omezení většina služeb Azure nepodporuje šifrování na straně serveru pomocí klíčů spravovaných serverem v hardwaru ovládaném zákazníkem.

### <a name="key-access"></a>Přístup ke klíči

Pokud se používá šifrování na straně serveru pomocí klíčů spravovaných službou v hardwaru ovládaném zákazníkem, klíče se udržují v systému nakonfigurovaném zákazníkem. Služby Azure, které podporují tento model, poskytují prostředky pro vytvoření zabezpečeného připojení k úložišti klíčů poskytovanému zákazníkem.

**Výhody**

- Úplná kontrola nad použitým kořenovým klíčem – šifrovací klíče se spravují pomocí zákaznického úložiště.
- Možnost šifrovat více služeb do jedné hlavní větve
- Může oddělit správu klíčů od celkového modelu správy pro službu.
- Může definovat službu a umístění klíčů v různých oblastech.

**Nevýhody**

- Plná zodpovědnost za úložiště klíčů, zabezpečení, výkon a dostupnost
- Plná zodpovědnost za správu přístupu ke klíčům
- Plná zodpovědnost za správu životního cyklu klíčů
- Významné náklady na instalaci, konfiguraci a průběžnou údržbu
- Zvýšená závislost na dostupnosti sítě mezi zákaznickým datovým centrem a datacentry Azure.

## <a name="supporting-services"></a>Podpůrné služby
Služby Azure, které podporují jednotlivé modely šifrování:

| Produkt, funkce nebo služba | Server-Side pomocí klíče Service-Managed   | Server-Side pomocí klíče Customer-Managed | Client-Side pomocí klíče Client-Managed  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **AI a strojové učení**      |                    |                    |                    |
| Azure Cognitive Search           | Yes                | Yes                | -                  |
| Azure Cognitive Services         | Yes                | Yes                | -                  |
| Azure Machine Learning           | Yes                | Yes                | -                  |
| Azure Machine Learning Studio    | Yes                | Verze Preview, RSA 2048-bit | -               |
| Content Moderator                | Yes                | Yes                | -                  |
| Rozpoznávání tváře                             | Yes                | Yes                | -                  |
| Language Understanding           | Yes                | Yes                | -                  |
| Personalizace                     | Yes                | Yes                | -                  |
| QnA Maker                        | Yes                | Yes                | -                  |
| Hlasové služby                  | Yes                | Yes                | -                  |
| Translator Text                  | Yes                | Yes                | -                  |
| Power BI                         | Yes                | Ano, RSA 4096-bit  | -                  |
| **Analýzy**                    |                    |                    |                    |
| Azure Stream Analytics           | Yes                | NENÍ K DISPOZICI\*              | -                  |
| Event Hubs                       | Yes                | Yes                | -                  |
| Funkce                        | Yes                | Yes                | -                  |
| Azure Analysis Services          | Ano                | -                  | -                  |
| Azure Data Catalog               | Yes                | -                  | -                  |
| Azure HDInsight                  | Yes                | Vše                | -                  |
| Azure Monitor Application Insights | Yes                | Yes                | -                  |
| Azure Monitor Log Analytics      | Yes                | Yes                | -                  |
| Průzkumník dat Azure              | Yes                | Yes                | -                  |
| Azure Data Factory               | Yes                | Yes                | -                  |
| Azure Data Lake Store            | Ano                | Ano, RSA 2048-bit  | -                  |
| **Containers**                   |                    |                    |                    |
| Azure Kubernetes Service         | Yes                | Yes                | -                  |
| Container Instances              | Yes                | Yes                | -                  |
| Container Registry               | Yes                | Yes                | -                  |
| **Výpočetní služby**                      |                    |                    |                    |
| Virtual Machines                 | Yes                | Yes                | -                  |
| Sada škálování virtuálních počítačů        | Yes                | Ano                | -                  |
| SAP HANA                         | Ano                | Yes                | -                  |
| App Service                      | Yes                | Ano\*\*            | -                  |
| Automation                       | Yes                | Ano\*\*            | -                  |
| Azure Functions                  | Yes                | Ano\*\*            | -                  |
| portál Azure                     | Yes                | Ano\*\*            | -                  |
| Logic Apps                       | Yes                | Yes                | -                  |
| Aplikace spravované v Azure       | Yes                | Ano\*\*            | -                  |
| Service Bus                      | Yes                | Yes                | -                  |
| Site Recovery                    | Yes                | Yes                | -                  |
| **Databáze**                    |                    |                    |                    |
| SQL Server na virtuálních počítačích   | Yes                | Yes                | Yes                |
| Azure SQL Database               | Yes                | Ano, RSA 3072-bit  | Yes                |
| Azure SQL Database pro MariaDB   | Yes                | -                  | -                  |
| Azure SQL Database pro MySQL     | Yes                | Yes                | -                  |
| Azure SQL Database pro PostgreSQL | Yes               | Yes                | -                  |
| Azure Synapse Analytics          | Yes                | Ano, RSA 3072-bit  | -                  |
| SQL Server Stretch Database      | Yes                | Ano, RSA 3072-bit  | Yes                |
| Table Storage                    | Yes                | Yes                | Yes                |
| Azure Cosmos DB                  | Ano                | Yes                | -                  |
| Azure Databricks                 | Yes                | Yes                | -                  |
| Azure Database Migration Service | Yes                | NENÍ K DISPOZICI\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Yes                | -                  | Yes                |
| Azure Repos                      | Yes                | -                  | Yes                |
| **Identita**                     |                    |                    |                    |
| Azure Active Directory           | Yes                | -                  | -                  |
| Azure Active Directory Domain Services | Yes          | Yes                | -                  |
| **Integrace**                  |                    |                    |                    |
| Service Bus                      | Yes                | Yes                | Yes                |
| Event Grid                       | Yes                | -                  | -                  |
| API Management                   | Yes                | -                  | -                  |
| **Služby IoT**                 |                    |                    |                    |
| IoT Hub                          | Yes                | Yes                | Yes                |
| IoT Hub Device Provisioning      | Yes                | Yes                | -                  |
| **Správa a zásady správného řízení**    |                    |                    |                    |
| Azure Site Recovery              | Yes                | -                  | -                  |
| Azure Migrate                    | Yes                | Yes                | -                  |
| **Média**                        |                    |                    |                    |
| Media Services                   | Yes                | -                  | Yes                |
| **Zabezpečení**                     |                    |                    |                    |
| Azure Security Center for IoT    | Yes                | Yes                | -                  |
| Azure Sentinel                   | Yes                | Yes                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Ano                | Yes                | Yes                |
| Blob Storage úrovně Premium             | Yes                | Yes                | Yes                |
| Disk Storage                     | Yes                | Yes                | -                  |
| Ultra Disk Storage               | Yes                | Yes                | -                  |
| Spravované Disk Storage             | Yes                | Yes                | -                  |
| File Storage                     | Ano                | Yes                | -                  |
| Premium Storage souboru             | Yes                | Yes                | -                  |
| Synchronizace souborů                        | Yes                | Yes                | -                  |
| Queue Storage                    | Yes                | Yes                | Yes                |
| Avere vFXT                       | Yes                | -                  | -                  |
| Azure Cache for Redis            | Yes                | NENÍ K DISPOZICI\*              | -                  |
| Azure NetApp Files               | Yes                | Yes                | -                  |
| Archiv služby Storage                  | Yes                | Yes                | -                  |
| StorSimple                       | Yes                | Yes                | Yes                |
| Azure Backup                     | Yes                | Yes                | Yes                |
| Data Box                         | Yes                | -                  | Yes                |
| Data Box Edge                    | Yes                | Yes                | -                  |

\* Tato služba neuchovává data. Přechodné mezipaměti, pokud jsou nějaké, se šifrují pomocí klíče Microsoftu.

\*\* Tato služba podporuje ukládání dat do vlastního Key Vault, účtu úložiště nebo jiné trvalé služby dat, která již podporuje šifrování Server-Side pomocí Customer-Managed Key.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [se používá šifrování v Azure](encryption-overview.md).
- Přečtěte si, jak Azure používá [dvojité šifrování](double-encryption.md) ke zmírnění hrozeb, které jsou součástí šifrování dat.
