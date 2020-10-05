---
title: Povolení Azure Machine Learning studia ve virtuální síti
titleSuffix: Azure Machine Learning
description: Pro přístup k datům uloženým v rámci virtuální sítě použijte Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 58395463c494a95a8842cddbe4d51544ce03d212
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713371"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Použití Azure Machine Learning studia ve službě Azure Virtual Network

V tomto článku se dozvíte, jak používat Azure Machine Learning Studio ve virtuální síti. Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Přístup k studiu z prostředku v rámci virtuální sítě.
> - Udělte studiu přístup k datům uloženým ve virtuální síti.
> - Seznamte se s tím, jak má Studio vliv na zabezpečení úložiště.

Tento článek je třetí částí série s pěti částmi, která vás provede zabezpečením Azure Machine Learningho pracovního postupu. Důrazně doporučujeme, abyste si přečetli [část One: VNet – přehled](how-to-network-security-overview.md) , abyste si nejdřív porozuměli celkové architektuře. 

Podívejte se na další články v této sérii:

[1. virtuální síť – přehled](how-to-network-security-overview.md)  >  [2 Zabezpečte pracovní prostor](how-to-secure-workspace-vnet.md)  >  [3. Zabezpečte školicí prostředí](how-to-secure-training-vnet.md)  >  [4. Zabezpečte prostředí Inferencing](how-to-secure-inferencing-vnet.md)  >  [5. Povolit funkci studia](how-to-enable-studio-virtual-network.md)


> [!IMPORTANT]
> I když většina sady Studio funguje s daty uloženými ve virtuální síti, integrované poznámkové bloky __ne__. Integrované poznámkové bloky nepodporují používání úložiště, které je ve virtuální síti. Místo toho můžete použít poznámkové bloky Jupyter z výpočetní instance. Další informace najdete v části [přístup k datům v poznámkovém bloku COMPUTE instance]() .


## <a name="prerequisites"></a>Předpoklady

+ Přečtěte si [Přehled zabezpečení sítě](how-to-network-security-overview.md) , abyste porozuměli běžným scénářům virtuální sítě a celkové architektuře virtuální sítě.

+ Již existující virtuální síť a podsíť, která se má použít.

+ Existující [Azure Machine Learning pracovní prostor s povoleným privátním odkazem](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Existující [účet Azure Storage přidaný do vaší virtuální sítě](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts).

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Přístup k studiu z prostředku uvnitř virtuální sítě

Pokud k studiu přistupujete z prostředku uvnitř virtuální sítě (například výpočetní instance nebo virtuální počítač), musíte z této virtuální sítě pro Studio dovolit odchozí přenosy. 

Pokud například používáte skupiny zabezpečení sítě (NSG) k omezení odchozího provozu, přidejte pravidlo do cíle __značky služby__ __AzureFrontDoor. front-end__.

## <a name="access-data-using-the-studio"></a>Přístup k datům pomocí studia

Po [Přidání účtu úložiště Azure do virtuální sítě](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)musíte svůj účet úložiště nakonfigurovat tak, aby používal [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) k udělení přístupu studia k vašim datům. Studio podporuje účty úložiště nakonfigurované pro použití koncových bodů služby nebo privátních koncových bodů. Účty úložiště ve výchozím nastavení používají koncové body služby. Postup povolení privátních koncových bodů pro úložiště najdete v tématu [použití privátních koncových bodů pro Azure Storage](../storage/common/storage-private-endpoints.md)

Pokud nepovolíte spravovanou identitu, zobrazí se tato chyba, navíc se `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` zakážou tyto operace:

* Náhled dat v studiu
* Vizualizujte data v návrháři.
* Odešlete experiment AutoML.
* Spusťte Popis projektu.

Studio podporuje čtení dat z následujících typů úložiště dat ve virtuální síti:

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="configure-datastores-to-use-managed-identity"></a>Konfigurace úložiště dat pro použití spravované identity

Azure Machine Learning používá [úložiště dat](concept-data.md#datastores) pro připojení k účtům úložiště. Pomocí následujících kroků můžete nakonfigurovat úložiště dat tak, aby používalo spravovanou identitu. 

1. V nástroji Studio vyberte __úložiště dat__.

1. Pokud chcete vytvořit nové úložiště dat, vyberte __+ nové úložiště dat__.

    Pokud chcete aktualizovat existující úložiště dat, vyberte úložiště dat a vyberte __Aktualizovat přihlašovací údaje__.

1. V nastavení úložiště dat vyberte __Ano__ , pokud  __chcete, aby služba Azure Machine Learning měla přístup k úložišti pomocí identity spravované pracovním prostorem__.


Tyto kroky přidají identitu spravovanou pracovním prostorem jako __čtecí__ službu úložiště pomocí řízení přístupu na základě prostředků Azure (RBAC). Přístup __Čtenář__ umožňuje pracovnímu prostoru načíst nastavení brány firewall a zajistit, aby data nezůstala virtuální síť.

> [!NOTE]
> Tyto změny mohou trvat až 10 minut, než se projeví.

## <a name="technical-notes-for-managed-identity"></a>Technické poznámky ke spravované identitě

Použití spravované identity pro přístup ke službám úložiště má vliv na některé z hlediska zabezpečení. Tyto požadavky jsou jedinečné pro typ účtu úložiště, ke kterému přistupujete. Tato část popisuje změny pro každý typ účtu úložiště.

### <a name="azure-blob-storage"></a>Azure Blob Storage

V případě __úložiště objektů BLOB v Azure__je identita spravovaná pracovním prostorem přidána také jako [čtecí modul objektů BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , aby mohla číst data z úložiště objektů BLOB.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Řízení přístupu Azure Data Lake Storage Gen2

K řízení přístupu k datům v rámci virtuální sítě můžete použít seznamy RBAC a seznam řízení přístupu (ACL) ve stylu POSIX.

Chcete-li použít RBAC, přidejte identitu spravovanou pracovním prostorem do role [čtečky dat objektů BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Další informace najdete v tématu [řízení přístupu na základě role v Azure](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control).

Aby bylo možné používat seznamy řízení přístupu (ACL), je možné přiřadit přístup stejným způsobem jako jakýkoli jiný princip zabezpečení. Další informace najdete v tématu [seznam řízení přístupu k souborům a adresářům](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Řízení přístupu Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 podporuje jenom seznamy řízení přístupu ve stylu POSIX. Přístup k identitám spravovaným v pracovním prostoru můžete přiřadit k prostředkům stejným způsobem jako jakýkoli jiný princip zabezpečení. Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database zahrnutý uživatel

Pokud chcete získat přístup k datům uloženým v Azure SQL Database pomocí spravované identity, musíte vytvořit uživatele obsahující SQL, který se mapuje na spravovanou identitu. Další informace o tom, jak vytvořit uživatele z externího poskytovatele, najdete v tématu [Vytvoření obsažených uživatelů mapovaných na identity Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Po vytvoření uživatele s omezením SQL udělte mu oprávnění pomocí [příkazu Grant T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-default-datastore"></a>Výchozí úložiště dat návrháře Azure Machine Learning

Návrhář používá účet úložiště připojený k vašemu pracovnímu prostoru k uložení výstupu ve výchozím nastavení. Můžete ji však zadat pro uložení výstupu do libovolného úložiště dat, ke kterému máte přístup. Pokud vaše prostředí používá virtuální sítě, můžete pomocí těchto ovládacích prvků zajistit, aby data zůstala zabezpečená a přístupná.

Nastavení nového výchozího úložiště pro kanál:

1. V konceptu kanálu vyberte **ikonu ozubeného kolečka pro nastavení** vedle názvu vašeho kanálu.
1. Vyberte **Vybrat výchozí úložiště dat**.
1. Zadejte nové úložiště dat.

Výchozí úložiště dat můžete také přepsat na základě jednotlivých modulů. Díky tomu budete mít kontrolu nad umístěním úložiště pro každý jednotlivý modul.

1. Vyberte modul, jehož výstup chcete zadat.
1. Rozbalte část **Nastavení výstupu** .
1. Vyberte **přepsat výchozí nastavení výstupu**.
1. Vyberte **nastavit výstupní nastavení**.
1. Zadejte nové úložiště dat.

## <a name="next-steps"></a>Další kroky

Tento článek je volitelnou součástí řady virtuálních sítí se čtyřmi částmi. Další informace o tom, jak zabezpečit virtuální síť, najdete v dalších článcích:

* [Část 1: Přehled virtuální sítě](how-to-network-security-overview.md)
* [Část 2: zabezpečení prostředků pracovního prostoru](how-to-secure-workspace-vnet.md)
* [Část 3: zabezpečení školicího prostředí](how-to-secure-training-vnet.md)
* [4. část: zabezpečení prostředí Inferencing](how-to-secure-inferencing-vnet.md)