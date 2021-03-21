---
title: Povolení Azure Machine Learning studia ve virtuální síti
titleSuffix: Azure Machine Learning
description: Naučte se, jak nakonfigurovat Azure Machine Learning Studio pro přístup k datům uloženým ve virtuální síti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperf-fy20q4, tracking-python
ms.openlocfilehash: da8007a651b62430055f263f082fabf2aa4bf610
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574284"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Použití Azure Machine Learning studia ve službě Azure Virtual Network

V tomto článku se dozvíte, jak používat Azure Machine Learning Studio ve virtuální síti. Studio zahrnuje funkce, jako je AutoML, Návrhář a popisek dat. Aby bylo možné používat tyto funkce ve virtuální síti, je nutné postupovat podle kroků v tomto článku.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> - Udělte studiu přístup k datům uloženým ve virtuální síti.
> - Přístup k studiu z prostředku v rámci virtuální sítě.
> - Zjistěte, jak Studio ovlivňuje zabezpečení úložiště.

Tento článek je třetí částí série s pěti částmi, která vás provede zabezpečením Azure Machine Learningho pracovního postupu. Důrazně doporučujeme, abyste si přečetli předchozí části a nastavili prostředí virtuální sítě.

Podívejte se na další články v této sérii:

[1. virtuální síť – přehled](how-to-network-security-overview.md)  >  [2 Zabezpečte pracovní prostor](how-to-secure-workspace-vnet.md)  >  [3. Zabezpečte školicí prostředí](how-to-secure-training-vnet.md)  >  [4. Zabezpečte prostředí Inferencing](how-to-secure-inferencing-vnet.md)  >  **5. Povolit funkci studia**


> [!IMPORTANT]
> Pokud je váš pracovní prostor v rámci __svrchovaného cloudu__, například Azure Government nebo Azure Čína 21Vianet, integrované poznámkové bloky _nepodporují použití_ úložiště, které je ve virtuální síti. Místo toho můžete na výpočetní instanci použít poznámkové bloky Jupyter. Další informace najdete v části [přístup k datům v poznámkovém bloku COMPUTE instance](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) .

## <a name="prerequisites"></a>Předpoklady

+ Přečtěte si [Přehled zabezpečení sítě](how-to-network-security-overview.md) a seznamte se s běžnými scénáři a architekturou virtuální sítě.

+ Již existující virtuální síť a podsíť, která se má použít.

+ Existující [Azure Machine Learning pracovní prostor s povoleným privátním odkazem](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Existující [účet Azure Storage přidaný do vaší virtuální sítě](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="configure-data-access-in-the-studio"></a>Konfigurace přístupu k datům v studiu

Některé funkce nástroje Studio jsou ve výchozím nastavení ve virtuální síti zakázané. Pokud chcete tyto funkce znovu povolit, musíte povolit spravovanou identitu pro účty úložiště, které chcete používat v studiu. 

Ve výchozím nastavení jsou ve virtuální síti zakázané tyto operace:

* Náhled dat v studiu
* Vizualizujte data v návrháři.
* Nasaďte model v Návrháři ([výchozí účet úložiště](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Odešlete experiment AutoML ([výchozí účet úložiště](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Spusťte Popis projektu.

Studio podporuje čtení dat z následujících typů úložiště dat ve virtuální síti:

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Konfigurace úložiště dat pro použití identity spravované v pracovním prostoru

Po přidání účtu Azure Storage do virtuální sítě pomocí [koncového bodu služby](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) nebo [privátního koncového bodu](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)je nutné nakonfigurovat úložiště dat tak, aby používalo [spravované ověřování identity](../active-directory/managed-identities-azure-resources/overview.md) . Tím umožníte, aby aplikace Studio měla přístup k datům ve vašem účtu úložiště.

Azure Machine Learning používá [úložiště dat](concept-data.md#datastores) pro připojení k účtům úložiště. Pomocí následujících kroků můžete nakonfigurovat úložiště dat pro použití spravované identity:

1. V nástroji Studio vyberte __úložiště dat__.

1. Pokud chcete aktualizovat existující úložiště dat, vyberte úložiště dat a vyberte __Aktualizovat přihlašovací údaje__.

    Pokud chcete vytvořit nové úložiště dat, vyberte __+ nové úložiště dat__.

1. V nastavení úložiště dat vyberte __Ano__ , pokud chcete  __použít spravovanou identitu pracovního prostoru pro náhled a profilaci dat v Azure Machine Learning Studiu__.

    ![Snímek obrazovky ukazující, jak povolit identitu spravovaného pracovního prostoru](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

Tyto kroky přidají identitu spravovanou pracovním prostorem jako __čtecí__ službu úložiště pomocí Azure RBAC. Přístup __Čtenář__ umožňuje pracovnímu prostoru načíst nastavení brány firewall, aby se zajistilo, že data neopustí virtuální síť. Změny se projeví až po dobu 10 minut.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>Povolit spravované ověřování identity pro výchozí účty úložiště

Každý Azure Machine Learning pracovní prostor má dva výchozí účty úložiště, výchozí účet Blob Storage a výchozí účet úložiště souborů, které jsou definovány při vytváření pracovního prostoru. Na stránce Správa **úložiště dat** můžete také nastavit nové výchozí hodnoty.

![Snímek obrazovky s informacemi o tom, kde je možné najít výchozí úložiště dat](./media/how-to-enable-studio-virtual-network/default-datastores.png)

V následující tabulce jsou popsány důvody, proč je nutné povolit spravované ověřování identity pro výchozí účty úložiště v pracovním prostoru.

|Účet úložiště  | Poznámky  |
|---------|---------|
|Výchozí úložiště objektů BLOB v pracovním prostoru| Ukládá assety modelu z návrháře. Pro nasazení modelů v Návrháři musíte povolit spravované ověřování identity na tomto účtu úložiště. <br> <br> Můžete vizualizovat a spustit kanál návrháře, pokud používá jiné než výchozí úložiště dat, které bylo nakonfigurováno pro použití spravované identity. Pokud se ale pokusíte nasadit vyškolený model bez povolené spravované identity ve výchozím úložišti dat, nasazení selže bez ohledu na to, jaké jiné úložiště dat se používá.|
|Výchozí úložiště souborů v pracovním prostoru| Ukládá prostředky experimentů AutoML. Abyste mohli odesílat AutoML experimenty, musíte na tomto účtu úložiště povolit spravované ověřování identity. |

> [!WARNING]
> K dispozici je známý problém, kdy výchozí úložiště souborů nevytvoří automaticky `azureml-filestore` složku, která je nutná k odeslání experimentů AutoML. K tomu dochází, když uživatelé přinášejí existující úložiště soubor k nastavení jako výchozí úložiště souboru během vytváření pracovního prostoru.
> 
> Chcete-li se tomuto problému vyhnout, máte dvě možnosti: 1) použijte výchozí úložiště úložišť, které se automaticky vytvoří při vytváření pracovního prostoru. 2) Chcete-li uvést vlastní úložiště, ujistěte se, že je úložiště typu úložiště mimo virtuální síť během vytváření pracovního prostoru. Po vytvoření pracovního prostoru přidejte účet úložiště do virtuální sítě.
>
> Pokud chcete tento problém vyřešit, odeberte účet úložiště z virtuální sítě a pak ho přidejte zpátky do virtuální sítě.

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Udělení přístupu ke spravované identitě spravovanému pracovnímu __prostoru k__ privátnímu odkazu na úložiště

Pokud váš účet úložiště Azure používá privátní koncový bod, musíte tomuto pracovnímu prostoru udělit přístup ke **čtečce** identit spravovanému pracovním prostorem. Další informace najdete v předdefinované roli [čtenáře](../role-based-access-control/built-in-roles.md#reader) . 

Pokud váš účet úložiště používá koncový bod služby, můžete tento krok přeskočit.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Přístup k studiu z prostředku uvnitř virtuální sítě

Pokud k studiu přistupujete z prostředku uvnitř virtuální sítě (například výpočetní instance nebo virtuální počítač), musíte z této virtuální sítě pro Studio dovolit odchozí přenosy. 

Pokud například používáte skupiny zabezpečení sítě (NSG) k omezení odchozího provozu, přidejte pravidlo do cíle __značky služby__ __AzureFrontDoor. front-end__.

## <a name="technical-notes-for-managed-identity"></a>Technické poznámky ke spravované identitě

Použití spravované identity pro přístup ke službám úložiště ovlivňuje požadavky na zabezpečení. Tato část popisuje změny pro každý typ účtu úložiště. 

Tyto požadavky jsou jedinečné pro __typ účtu úložiště__ , ke kterému přistupujete.

### <a name="azure-blob-storage"></a>Azure Blob Storage

V případě __úložiště objektů BLOB v Azure__ je identita spravovaná pracovním prostorem přidána také jako [čtecí modul objektů BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , aby mohla číst data z úložiště objektů BLOB.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Řízení přístupu Azure Data Lake Storage Gen2

K řízení přístupu k datům v rámci virtuální sítě můžete použít seznamy řízení přístupu (ACL) pro Azure RBAC i POSIX.

Chcete-li použít Azure RBAC, přidejte identitu spravovanou pracovním prostorem do role [čtečky dat objektů BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Další informace najdete v tématu [řízení přístupu na základě role v Azure](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Aby bylo možné používat seznamy řízení přístupu (ACL), je možné přiřadit přístup stejným způsobem jako jakýkoli jiný princip zabezpečení. Další informace najdete v tématu [seznam řízení přístupu k souborům a adresářům](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Řízení přístupu Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 podporuje jenom seznamy řízení přístupu ve stylu POSIX. Přístup k identitám spravovaným v pracovním prostoru můžete přiřadit k prostředkům stejným způsobem jako jakýkoli jiný princip zabezpečení. Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database zahrnutý uživatel

Pokud chcete získat přístup k datům uloženým v Azure SQL Database pomocí spravované identity, musíte vytvořit uživatele obsahující SQL, který se mapuje na spravovanou identitu. Další informace o tom, jak vytvořit uživatele z externího poskytovatele, najdete v tématu [Vytvoření obsažených uživatelů mapovaných na identity Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Po vytvoření uživatele s omezením SQL udělte mu oprávnění pomocí [příkazu Grant T-SQL](/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Výstup zprostředkujícího modulu Azure Machine Learning návrháře

Můžete zadat umístění výstupu pro libovolný modul v návrháři. Tato operace slouží k uložení mezilehlých datových sad do samostatného umístění pro účely zabezpečení, protokolování nebo auditování. Určení výstupu:

1. Vyberte modul, jehož výstup chcete zadat.
1. V podokně nastavení modulu, které se zobrazí vpravo, vyberte **Nastavení výstupu**.
1. Zadejte úložiště dat, které chcete použít pro jednotlivé výstupy modulu.
 
Ujistěte se, že máte přístup k zprostředkujícím účtům úložiště ve vaší virtuální síti. V opačném případě se kanál nezdařil.

Měli byste taky [Povolit spravované ověřování identity](#configure-datastores-to-use-workspace-managed-identity) pro dočasné účty úložiště k vizualizaci výstupních dat.

## <a name="next-steps"></a>Další kroky

Tento článek je 5. část řady virtuálních sítí s pěti částmi. Další informace o tom, jak zabezpečit virtuální síť, najdete v dalších článcích:

* [Část 1: Přehled virtuální sítě](how-to-network-security-overview.md)
* [Část 2: zabezpečení prostředků pracovního prostoru](how-to-secure-workspace-vnet.md)
* [Část 3: zabezpečení školicího prostředí](how-to-secure-training-vnet.md)
* [4. část: zabezpečení prostředí Inferencing](how-to-secure-inferencing-vnet.md)

Přečtěte si také článek o používání [vlastního DNS](how-to-custom-dns.md) pro překlad IP adres.