---
title: Šifrování Azure synapse Analytics
description: Článek, který vysvětluje šifrování ve službě Azure synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: d9a9d3c303739e68b5b8ef28053d6cf0b071f955
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501052"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Šifrování pracovních prostorů služby Azure synapse Analytics

Tento článek popisuje:
* Šifrování neaktivních dat v pracovních prostorech synapse Analytics.
* Konfigurace pracovních prostorů synapse pro povolení šifrování pomocí spravovaného klíče zákazníka.
* Správa klíčů používaných k šifrování dat v pracovních prostorech.

## <a name="encryption-of-data-at-rest"></a>Šifrování dat v klidovém umístění

Úplné řešení v klidovém stavu zajišťuje, aby se data nikdy nezachovala v nešifrované podobě. Dvojité Šifrování neaktivních dat snižuje hrozby se dvěma, oddělenými vrstvami šifrování, aby se chránily proti ohrožením jakékoli jedné vrstvy. Azure synapse Analytics nabízí druhou vrstvu šifrování pro data v pracovním prostoru pomocí klíče spravovaného zákazníkem. Tento klíč je chráněn ve vašem [Azure Key Vault](../../key-vault/general/overview.md), což vám umožňuje převzít vlastnictví správy a rotace klíčů.

První vrstva šifrování pro služby Azure je povolená pomocí klíčů spravovaných platformou. Ve výchozím nastavení jsou disky Azure a data v Azure Storage účty automaticky šifrovány v klidovém stavu. Přečtěte si další informace o tom, jak se šifrování používá v Microsoft Azure v tématu [Přehled šifrování Azure](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Šifrování Azure synapse

Tato část vám pomůže lépe porozumět tomu, jak se povoluje a vynutilo šifrování pomocí klíče spravovaného zákazníkem v pracovních prostorech synapse. Toto šifrování používá existující klíče nebo nové klíče vygenerované v Azure Key Vault. Jeden klíč se používá k šifrování všech dat v pracovním prostoru. Pracovní prostory synapse podporují klíče RSA s 2048 a 3072 klíčů pro velikost bajtů.

> [!NOTE]
> Pracovní prostory synapse nepodporují šifrování pomocí klíčů ECC (Elliptic-Curve Cryptography).

Data v následujících součástech synapse se šifrují pomocí klíče spravovaného zákazníkem nakonfigurovaným na úrovni pracovního prostoru:
* Fondy SQL
 * Vyhrazené fondy SQL
 * Fondy SQL bez serveru
* Fondy Apache Spark
* Azure Data Factory modulu runtime integrace, kanálů, datových sad.

## <a name="workspace-encryption-configuration"></a>Konfigurace šifrování pracovního prostoru

Pracovní prostory je možné nakonfigurovat tak, aby v době vytváření pracovního prostoru povolily dvojité šifrování pomocí klíče spravovaného zákazníkem. Pokud vytváříte nový pracovní prostor, zaškrtněte na kartě zabezpečení možnost Povolit dvojité šifrování pomocí klíče spravovaného zákazníkem. Můžete zvolit, že chcete zadat identifikátor URI identifikátoru klíče nebo vybrat ze seznamu trezorů klíčů ve **stejné oblasti** jako pracovní prostor. Key Vault musí mít **povolenou ochranu vyprázdnění**.

> [!IMPORTANT]
> Po vytvoření pracovního prostoru nelze změnit nastavení konfigurace pro dvojité šifrování.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Tento diagram znázorňuje možnost, kterou je třeba vybrat, aby byl pracovní prostor pro dvojité šifrování s klíčem spravovaným zákazníkem povolen.":::

### <a name="key-access-and-workspace-activation"></a>Přístup k klíči a aktivace pracovního prostoru

Model šifrování Azure synapse pomocí klíčů spravovaných zákazníkem vyžaduje, aby se v pracovním prostoru při přístupu k klíčům v Azure Key Vault zašifroval a dešifroval podle potřeby. Klíče jsou k pracovnímu prostoru přístupné buď prostřednictvím zásad přístupu, nebo Azure Key Vault přístupu RBAC ([Preview](../../key-vault/general/rbac-guide.md)). Při udělování oprávnění prostřednictvím zásad přístupu Azure Key Vault zvolte možnost [pouze aplikace](../../key-vault/general/secure-your-key-vault.md#key-vault-authentication-options) při vytváření zásad (vyberte spravovanou identitu pracovního prostoru a nepřidejte ji jako autorizovanou aplikaci).

 Aby bylo možné pracovní prostor aktivovat, musí být v trezoru klíčů udělena oprávnění, která potřebuje. Tato fáze přístupu k aktivaci pracovního prostoru zajišťuje, aby data v pracovním prostoru byla zašifrovaná pomocí klíče spravovaného zákazníkem. Upozorňujeme, že šifrování může být pro vyhrazené fondy SQL povolené nebo zakázané – ve výchozím nastavení není každý fond povolený šifrování.

#### <a name="permissions"></a>Oprávnění

K šifrování nebo dešifrování neaktivních dat musí mít spravovaná identita v pracovním prostoru následující oprávnění:
* WrapKey (pro vložení klíče do Key Vault při vytváření nového klíče).
* UnwrapKey (k získání klíče pro dešifrování).
* Získat (pro čtení veřejné části klíče)

#### <a name="workspace-activation"></a>Aktivace pracovního prostoru

Po vytvoření pracovního prostoru (s povoleným dvojitým šifrováním) zůstane stav "čeká", dokud nebude aktivace úspěšná. Aby bylo možné plně využívat všechny funkce, musí být tento pracovní prostor aktivovaný. Po úspěšné aktivaci můžete například vytvořit nový vyhrazený fond SQL. Udělte pracovnímu prostoru přístup k identitě spravovaným pracovním prostorem a klikněte na aktivační odkaz v pracovním prostoru Azure Portal banner. Po úspěšném dokončení aktivace je váš pracovní prostor připravený k použití s jistotou, že všechna data v nich jsou chráněná pomocí vašeho klíče spravovaného zákazníkem. Jak bylo uvedeno dříve, Trezor klíčů musí mít povolenou ochranu vyprázdněním, aby aktivace proběhla úspěšně.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Tento diagram zobrazuje banner s aktivačním odkazem pro pracovní prostor.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Správa klíče spravovaného zákazníkem pracovního prostoru 

Můžete změnit klíč spravovaný zákazníkem, který slouží k šifrování dat ze stránky **šifrování** v Azure Portal. V tomto případě můžete vybrat nový klíč pomocí identifikátoru klíče nebo vybrat z trezorů klíčů, ke kterým máte přístup, ve stejné oblasti jako pracovní prostor. Pokud zvolíte klíč v jiném trezoru klíčů z těch, které se dřív používaly, přidělte spravovanému pracovnímu prostoru oprávnění "získat", "Zalamovat" a "rozbalit" do nového trezoru klíčů. Pracovní prostor ověří svůj přístup k novému trezoru klíčů a všechna data v pracovním prostoru budou znovu zašifrována pomocí nového klíče.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Tento diagram znázorňuje část šifrování pracovního prostoru v Azure Portal.":::

>[!IMPORTANT]
>Při změně šifrovacího klíče pracovního prostoru si klíč zachovejte, dokud ho nenahradíte v pracovním prostoru novým klíčem. To umožňuje dešifrovat data se starým klíčem předtím, než se znovu zašifruje pomocí nového klíče.

Zásady Azure Key trezorů pro automatické, pravidelné střídání klíčů nebo akce na klíčích mají za následek vytváření nových verzí klíčů. Můžete zvolit, aby se všechna data v pracovním prostoru znovu zašifroval na nejnovější verzi aktivního klíče. Chcete-li znovu zašifrovat, změňte klíč v Azure Portal na dočasný klíč a pak přepněte zpět na klíč, který chcete použít pro šifrování. Chcete-li například aktualizovat šifrování dat pomocí nejnovější verze služby Active Key Klíč1, změňte klíč spravovaný zákazníkem na dočasný klíč, key2. Počkejte na dokončení šifrování pomocí key2. Pak přepněte klíč spravovaný zákazníkem pracovního prostoru zpátky na Klíč1 – data v pracovním prostoru se znovu zašifrují pomocí nejnovější verze Klíč1.

> [!NOTE]
> Azure synapse Analytics aktivně pracuje na přidávání funkcí, které automaticky znovu šifrují data při vytváření nových verzí klíčů. Dokud nebude tato funkce k dispozici, abyste zajistili konzistenci ve vašem pracovním prostoru, vynuťte opětovné šifrování dat pomocí výše popsaného procesu.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>transparentní šifrování dat SQL pomocí klíčů spravovaných službou

SQL transparentní šifrování dat (TDE) je k dispozici pro vyhrazené fondy SQL v pracovních prostorech, které *nejsou* pro šifrování typu Double povoleny. V tomto typu pracovního prostoru se klíč spravovaný službou používá k poskytnutí dvojitého šifrování dat ve vyhrazených fondech SQL. TDE s klíčem spravovaným službou se dá povolit nebo zakázat pro jednotlivé vyhrazené fondy SQL.

## <a name="next-steps"></a>Další kroky

[Implementace ochrany šifrování pro pracovní prostory synapse pomocí integrovaných zásad Azure](../policy-reference.md)

