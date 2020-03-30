---
title: Jak používat sdk pro Android
description: Jak používat Azure Mobile Apps SDK pro Android
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 52e91d900ce0f22862904695ba8adf463219c469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249383"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Jak používat Azure Mobile Apps SDK pro Android

Tato příručka ukazuje, jak pomocí sady SDK pro mobilní aplikace pro androidy implementovat běžné scénáře, například:

* Dotazování na data (vkládání, aktualizace a odstranění).
* ověřování
* Zpracování chyb.
* Přizpůsobení klienta.

Tato příručka se zaměřuje na straně klienta Android SDK.  Další informace o sadách SDK na straně serveru pro mobilní aplikace naleznete v [tématu Práce s sadou Back-End .NET SDK][10] nebo [Jak používat back-endovou sadu Node.js][11].

## <a name="reference-documentation"></a>Referenční dokumentace

Odkaz na [rozhraní JAVADocs API][12] pro klientskou knihovnu Android najdete na GitHubu.

## <a name="supported-platforms"></a>Podporované platformy

Sada Azure Mobile Apps SDK pro Android podporuje úrovně rozhraní API 19 až 24 (KitKat přes Nougat) pro faktory tvaru telefonu a tabletu.  Ověřování, zejména využívá společný přístup webového rámce ke shromažďování přihlašovacích údajů.  Ověřování toku serveru nefunguje se zařízeními s malým tvarovým faktorem, jako jsou hodinky.

## <a name="setup-and-prerequisites"></a>Nastavení a požadavky

Dokončete kurz [rychlého spuštění mobilních aplikací.](app-service-mobile-android-get-started.md)  Tato úloha zajišťuje splnění všech předpokladů pro vývoj mobilních aplikací Azure.  Úvodní příručka vám také pomůže nakonfigurovat účet a vytvořit první back-end mobilní aplikace.

Pokud se rozhodnete nedokončit kurz rychlého startu, proveďte následující úkoly:

* [vytvořte back-end mobilní aplikace,][13] který se bude používat s aplikací pro Android.
* V Android Studio [aktualizujte soubory sestavení Gradle](#gradle-build).
* [Povolit oprávnění k Internetu](#enable-internet).

### <a name="update-the-gradle-build-file"></a><a name="gradle-build"></a>Aktualizace souboru sestavení Gradle

Změňte oba soubory **build.gradle:**

1. Přidejte tento kód do souboru **build.gradle** na úrovni *projektu:*

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. Přidejte tento kód do souboru **build.gradle** na úrovni *aplikace modulu* uvnitř *značky závislostí:*

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    V současné době nejnovější verze je 3.4.0. Podporované verze jsou uvedeny [na bintray][14].

### <a name="enable-internet-permission"></a><a name="enable-internet"></a>Povolení oprávnění k Internetu

Pro přístup k Azure musí mít vaše aplikace povolené oprávnění k INTERNETU. Pokud ještě není povolená, přidejte do souboru **AndroidManifest.xml** následující řádek kódu:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Vytvoření připojení klienta

Azure Mobile Apps poskytuje pro vaši mobilní aplikaci čtyři funkce:

* Přístup k datům a offline synchronizace se službou Azure Mobile Apps Service.
* Volání vlastních api napsaných pomocí sady Azure Mobile Apps Server SDK.
* Ověřování pomocí ověřování a autorizace služby Azure App Service.
* Registrace nabízených oznámení pomocí center oznámení.

Každá z těchto funkcí nejprve `MobileServiceClient` vyžaduje vytvoření objektu.  Pouze `MobileServiceClient` jeden objekt by měl být vytvořen v rámci mobilního klienta (to znamená, že by měl být singleton vzor).  Vytvoření objektu: `MobileServiceClient`

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

Jedná `<MobileAppUrl>` se o řetězec nebo objekt URL, který odkazuje na váš mobilní back-end.  Pokud používáte Azure App Service k hostování mobilního back-endu, ujistěte se, že používáte zabezpečenou `https://` verzi adresy URL.

Klient také vyžaduje přístup k Aktivita `this` nebo Kontext - parametr v příkladu.  MobileServiceClient konstrukce by mělo `onCreate()` dojít v rámci metody `AndroidManifest.xml` aktivity odkazuje v souboru.

Jako osvědčený postup byste měli abstraktní komunikaci serveru do vlastní třídy (singleton-pattern).  V takovém případě byste měli předat aktivitu v rámci konstruktoru odpovídajícím způsobem nakonfigurovat službu.  Například:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Nyní můžete `AzureServiceAdapter.Initialize(this);` volat `onCreate()` metodu hlavní aktivity.  Všechny ostatní metody, které potřebují `AzureServiceAdapter.getInstance();` přístup ke klientovi použít k získání odkazu na servisní adaptér.

## <a name="data-operations"></a>Operace s daty

Jádrem sady Azure Mobile Apps SDK je poskytovat přístup k datům uloženým v rámci SQL Azure v back-endu mobilní aplikace.  K těmto datům můžete přistupovat pomocí tříd silného typu (upřednostňované) nebo dotazů bez typu (nedoporučuje se).  Převážná část této části se zabývá pomocí silného typu třídy.

### <a name="define-client-data-classes"></a>Definování tříd dat klienta

Chcete-li získat přístup k datům z tabulek SQL Azure, definujte třídy dat klienta, které odpovídají tabulkám v back-endu mobilní aplikace. Příklady v tomto tématu předpokládají tabulku s názvem **MyDataTable**, která má následující sloupce:

* id
* text
* Kompletní

Odpovídající zadaný objekt na straně klienta se nachází v souboru s názvem **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Pro každé pole, které přidáte, přidejte metody getter a setter.  Pokud tabulka SQL Azure obsahuje více sloupců, přidejte odpovídající pole do této třídy.  Pokud například dto (objekt přenosu dat) měl celý sloupec Priorita, můžete přidat toto pole spolu s jeho metodami getter a setter:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Informace o tom, jak vytvořit další tabulky v back-endu mobilních aplikací, najdete v tématu [Postup: Definování řadiče tabulky][15] (back-end.NET) nebo [Definování tabulek pomocí dynamického schématu][16] (back-end Node.js).

Back-endová tabulka mobilních aplikací Azure definuje pět speciálních polí, z nichž čtyři jsou klientům k dispozici:

* `String id`: Globálně jedinečné ID záznamu.  Jako osvědčený postup proveďte id řetězcové reprezentace objektu [UUID.][17]
* `DateTimeOffset updatedAt`: Datum a čas poslední aktualizace.  Pole updatedAt je nastaveno serverem a nikdy by nemělo být nastaveno klientským kódem.
* `DateTimeOffset createdAt`: Datum a čas, kdy byl objekt vytvořen.  Pole createdAt je nastaveno serverem a nikdy by nemělo být nastaveno klientským kódem.
* `byte[] version`: Obvykle reprezentovánjako řetězec, verze je také nastavena serverem.
* `boolean deleted`: Označuje, že záznam byl odstraněn, ale ještě nebyl vymazán.  Nepoužívejte `deleted` jako vlastnost ve vaší třídě.

Pole `id` je povinné.  Pole `updatedAt` a `version` pole se používají pro synchronizaci offline (pro přírůstkovou synchronizaci a řešení konfliktů).  Toto `createdAt` pole je referenční pole a klient je nepoužívá.  Názvy jsou "přes-the-wire" názvy vlastností a nejsou nastavitelné.  Můžete však vytvořit mapování mezi objektem a názvy "napříč drátem" pomocí [knihovny gson.][3]  Například:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Vytvoření odkazu na tabulku

Chcete-li získat přístup k tabulce, nejprve vytvořte objekt [MobileServiceTable][8] voláním metody **getTable** na [klientovi MobileServiceClient][9].  Tato metoda má dvě přetížení:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

V následujícím kódu je **mClient** odkazem na objekt MobileServiceClient.  První přetížení se používá, kde název třídy a název tabulky jsou stejné a je ten, který se používá v rychlém startu:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Druhé přetížení se používá, pokud se název tabulky liší od názvu třídy: první parametr je název tabulky.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query-a-backend-table"></a><a name="query"></a>Dotaz na back-endovou tabulku

Nejprve získat odkaz na tabulku.  Potom spusťte dotaz na odkaz na tabulku.  Dotaz je libovolná kombinace:

* `.where()` [Klauzule filtru](#filtering).
* `.orderBy()` [Objednávková doložka](#sorting).
* `.select()` [Klauzule pro výběr pole](#selection).
* A `.skip()` `.top()` a pro [stránkované výsledky](#paging).

Klauzule musí být uvedeny v předchozím pořadí.

### <a name="filtering-results"></a><a name="filter"></a>Filtrování výsledků

Obecná forma dotazu je:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Předchozí příklad vrátí všechny výsledky (až do maximální velikosti stránky nastavené serverem).  Metoda `.execute()` spustí dotaz na back-endu.  Dotaz je převeden na dotaz [OData v3][19] před přenosem do back-endu mobilní aplikace.  Při příjmu back-end mobilních aplikací převede dotaz na příkaz SQL před jeho spuštěním na instanci SQL Azure.  Vzhledem k tomu, `.execute()` že aktivita sítě trvá nějakou dobu, Metoda vrátí [`ListenableFuture<E>`][18].

### <a name="filter-returned-data"></a><a name="filtering"></a>Filtrvrácená data

Následující spuštění dotazu vrátí všechny položky z tabulky **ToDoItem,** kde **se úplné** rovná **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** je odkaz na tabulku mobilních služeb, kterou jsme vytvořili dříve.

Definujte filtr pomocí volání metody **where** v odkazu na tabulku. Metoda **where** následuje metoda **pole** následovaná metodou, která určuje logický predikát. Možné predikát metody zahrnují **eq** (rovná se), **ne** (není rovno), **gt** (větší než), **ge** (větší než nebo rovno), **lt** (menší než), **le** (menší než nebo rovno). Tyto metody umožňují porovnat číselná a řetězcová pole s konkrétními hodnotami.

Můžete filtrovat podle dat. Následující metody umožňují porovnat celé pole data nebo jeho části: **rok**, **měsíc**, **den**, **hodinu**, **minutu**a **druhou**. Následující příklad přidá filtr pro položky, jejichž *datum splatnosti* se rovná 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Následující metody podporují komplexní filtry na řetězcová pole: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim**a **length**. Následující příklad filtruje řádky tabulky, kde *sloupec textu* začíná "PRI0".

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

V číselnápole jsou podporovány následující metody operátoru: **přidat**, **pod**, **mul**, **div**, **mod**, **podlaha**, **strop**a **zaoblení**. Následující příklad filtruje řádky tabulky, kde doba **trvání** je sudé číslo.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Predikáty můžete kombinovat s těmito logickými metodami: **a**, **nebo** a **ne**. Následující příklad kombinuje dva z předchozích příkladů.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Logické operátory seskupit a vnořit:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Podrobnější diskusi a příklady filtrování najdete v tématu [Zkoumání bohatosti modelu dotazu klienta Android][20].

### <a name="sort-returned-data"></a><a name="sorting"></a>Řazení vrácených dat

Následující kód vrátí všechny položky z tabulky **ToDoItems** seřazené vzestupně podle *textového* pole. *mToDoTable* je odkaz na back-endovou tabulku, kterou jste vytvořili dříve:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

První parametr **metody orderBy** je řetězec rovnající se názvu pole, na kterém chcete řadit. Druhý parametr používá **Výčet QueryOrder** k určení, zda se má řadit vzestupně nebo sestupně.  Pokud filtrujete pomocí metody ***where,*** musí být metoda ***where*** vyvolána před metodou ***orderBy.***

### <a name="select-specific-columns"></a><a name="selection"></a>Výběr konkrétních sloupců

Následující kód ukazuje, jak vrátit všechny položky z tabulky **ToDoItems**, ale zobrazí pouze **kompletní** a **textová** pole. **mToDoTable** je odkaz na back-endovou tabulku, kterou jsme vytvořili dříve.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametry funkce select jsou názvy řetězců sloupců tabulky, které chcete vrátit.  Select **select** Metoda musí dodržovat metody, jako **je kde** a **orderBy**. To může být následováno stránkování metody, jako **je přeskočit** a **top**.

### <a name="return-data-in-pages"></a><a name="paging"></a>Vrácení dat na stránkách

Data jsou **vždy** vrácena na stránkách.  Server nastavuje maximální počet vrácených záznamů.  Pokud klient požaduje více záznamů, vrátí server maximální počet záznamů.  Ve výchozím nastavení je maximální velikost stránky na serveru 50 záznamů.

První příklad ukazuje, jak vybrat prvních pět položek z tabulky. Dotaz vrátí položky z tabulky **ToDoItems**. **mToDoTable** je odkaz na back-endovou tabulku, kterou jste vytvořili dříve:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Tady je dotaz, který přeskočí prvních pět položek a vrátí dalších pět:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Pokud chcete získat všechny záznamy v tabulce, implementujte kód k iteratu na všech stránkách:

```java
List<MyDataModel> results = new ArrayList<>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Požadavek na všechny záznamy pomocí této metody vytvoří minimálně dva požadavky na back-end mobilních aplikací.

> [!TIP]
> Výběr správné velikosti stránky je rovnováha mezi využitím paměti, zatímco požadavek se děje, využití šířky pásma a zpoždění při přijímání dat úplně.  Výchozí hodnota (50 záznamů) je vhodná pro všechna zařízení.  Pokud pracujete výhradně na větších paměťových zařízeních, zvyšte na 500.  Zjistili jsme, že zvětšení velikosti stránky nad 500 záznamů má za následek nepřijatelné zpoždění a velké problémy s pamětí.

### <a name="how-to-concatenate-query-methods"></a><a name="chaining"></a>Postup: Zřetězit metody dotazů

Metody používané při dotazování back-endtabulech lze zřetězit. Řetězení query metody umožňuje vybrat konkrétní sloupce filtrované řádky, které jsou seřazeny a stránkované. Můžete vytvořit složité logické filtry.  Každá metoda dotazu vrátí objekt Query. Chcete-li ukončit řadu metod a skutečně spustit dotaz, volání metody **spuštění.** Například:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Zřetězené metody dotazu musí být seřazeny takto:

1. Filtrování (**kde**) metody.
2. Řazení (**orderBy**) metody.
3. Metody výběru (**výběr).**
4. stránkovací (**přeskočit** a **top)** metody.

## <a name="bind-data-to-the-user-interface"></a><a name="binding"></a>Vazba dat s uživatelským rozhraním

Datová vazba zahrnuje tři složky:

* Zdroj dat
* Rozložení obrazovky
* Adaptér, který spojuje dva dohromady.

V našem ukázkovém kódu vrátíme data z tabulky **ToDoItem** pro mobilní aplikace SQL Azure do pole. Tato aktivita je běžný vzor pro datové aplikace.  Databázové dotazy často vrátí kolekci řádků, které klient získá v seznamu nebo poli. V této ukázce je pole zdrojem dat.  Kód určuje rozložení obrazovky, které definuje zobrazení dat, která se zobrazí v zařízení.  Tyto dva jsou vázány společně s adaptérem, který v tomto kódu je rozšíření **ArrayAdapter&lt;&gt; ToDoItem** třídy.

#### <a name="define-the-layout"></a><a name="layout"></a>Definování rozložení

Rozložení je definováno několika úryvky kódu XML. Vzhledem k existující rozložení, následující kód představuje **ListView** chceme naplnit s daty serveru.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

V předchozím kódu atribut *listitem* určuje id rozložení pro jednotlivé řádky v seznamu. Tento kód určuje zaškrtávací políčko a jeho přidružený text a získá vytvořena instance jednou pro každou položku v seznamu. Toto rozložení nezobrazuje pole **id** a složitější rozložení by určilo další pole v zobrazení. Tento kód je v souboru **row_list_to_do.xml.**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="define-the-adapter"></a><a name="adapter"></a>Definování adaptéru
Vzhledem k tomu, že zdrojem dat našeho zobrazení je pole **ToDoItem**, jsme podtřídy náš adaptér z **&lt;ArrayAdapter ToDoItem&gt; ** třídy. Tato podtřída vytvoří zobrazení pro každý **ToDoItem** pomocí **rozložení row_list_to_do.**  V našem kódu definujeme následující třídu, která je rozšířením třídy **&lt;ArrayAdapter E:&gt; **

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Přepsat adaptéry **getView** metoda. Například:

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

V naší aktivitě vytváříme instanci této třídy takto:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Druhý parametr konstruktoru ToDoItemAdapter je odkaz na rozložení. Nyní můžeme vytvořit instanci **ListView** a přiřadit adaptér **listView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-the-adapter-to-bind-to-the-ui"></a><a name="use-adapter"></a>Použití adaptéru k vazbě na ui

Nyní jste připraveni k použití datové vazby. Následující kód ukazuje, jak získat položky v tabulce a vyplní místní adaptér s vrácené položky.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Volání adaptéru při každé úpravě tabulky **ToDoItem.** Vzhledem k tomu, že změny jsou prováděny na základě záznamu podle záznamu, zpracovat jeden řádek namísto kolekce. Při vložení položky volejte metodu **add** na adaptéru; při odstraňování volejte metodu **remove.**

Úplný příklad najdete v [projektu Android Quickstart Project][21].

## <a name="insert-data-into-the-backend"></a><a name="inserting"></a>Vložení dat do back-endu

Vytvořte instanci třídy *ToDoItem* a nastavte její vlastnosti.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Potom pomocí **insert()** vložte objekt:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Vrácená entita odpovídá datům vloženým do back-endové tabulky, včetně `createdAt` `updatedAt`ID `version` a dalších hodnot (například , a polí) nastavených v back-endu.

Tabulky mobilních aplikací vyžadují sloupec primárního klíče s názvem **ID**. Tento sloupec musí být řetězec. Výchozí hodnota sloupce ID je identifikátor GUID.  Můžete zadat další jedinečné hodnoty, například e-mailové adresy nebo uživatelská jména. Pokud není pro vložený záznam k dispozici hodnota ID řetězce, back-end vygeneruje nový identifikátor GUID.

Hodnoty ID řetězce poskytují následující výhody:

* ID mohou být generovány bez provedení zpáteční cestu do databáze.
* Záznamy se snadněji slučují z různých tabulek nebo databází.
* Hodnoty ID se lépe integrují s logikou aplikace.

Hodnoty ID řetězce jsou **vyžadovány** pro podporu synchronizace offline.  Id nelze změnit, jakmile je uložen v databázi back-end.

## <a name="update-data-in-a-mobile-app"></a><a name="updating"></a>Aktualizace dat v mobilní aplikaci

Chcete-li aktualizovat data v tabulce, předajte nový objekt metodě **update().**

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

V tomto příkladu je *položka* odkazem na řádek v tabulce *ToDoItem,* ve kterém byly provedeny některé změny.  Řádek se stejným **id** je aktualizován.

## <a name="delete-data-in-a-mobile-app"></a><a name="deleting"></a>Odstranění dat v mobilní aplikaci

Následující kód ukazuje, jak odstranit data z tabulky zadáním datového objektu.

```java
mToDoTable
    .delete(item);
```

Položku můžete odstranit také zadáním pole **id** řádku, který chcete odstranit.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="look-up-a-specific-item-by-id"></a><a name="lookup"></a>Vyhledat konkrétní položku podle ID

Vyhledejte položku s určitým polem **id** metodou **lookUp():**

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="how-to-work-with-untyped-data"></a><a name="untyped"></a>Postup: Práce s netypovými daty

Netypový programovací model poskytuje přesnou kontrolu nad serializací JSON.  Existují některé běžné scénáře, kde můžete chtít použít netypový programovací model. Pokud například back-endová tabulka obsahuje mnoho sloupců a stačí odkazovat pouze na podmnožinu sloupců.  Zadaný model vyžaduje definování všech sloupců definovaných v back-endu mobilní aplikace ve vaší datové třídě.  Většina volání rozhraní API pro přístup k datům jsou podobné zadali programovací volání. Hlavní rozdíl je, že v netypovém modelu vyvoláte metody na objektu **MobileServiceJsonTable** namísto objektu **MobileServiceTable.**

### <a name="create-an-instance-of-an-untyped-table"></a><a name="json_instance"></a>Vytvoření instance netypové tabulky

Podobně jako zadaný model, začnete tím, že získáte odkaz na tabulku, ale v tomto případě je to **Objekt MobileServicesJsonTable.** Získat odkaz voláním **metody getTable** v instanci klienta:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Jakmile jste vytvořili instanci **MobileServiceJsonTable**, má prakticky stejné rozhraní API k dispozici jako s zadaným programovacím modelem. V některých případech metody trvat netypový parametr namísto zadali parametr.

### <a name="insert-into-an-untyped-table"></a><a name="json_insert"></a>Vložení do netypové tabulky
Následující kód ukazuje, jak provést vložení. Prvním krokem je vytvoření [JsonObject][1], který je součástí [knihovny gson.][3]

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Potom použijte **insert()** vložit netypový objekt do tabulky.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Pokud potřebujete získat ID vloženého objektu, použijte metodu **getAsJsonPrimitive().**

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="delete-from-an-untyped-table"></a><a name="json_delete"></a>Odstranění z netypové tabulky
Následující kód ukazuje, jak odstranit instanci, v tomto případě stejnou instanci **JsonObject,** která byla vytvořena v příkladu předchozí *vložit.* Kód je stejný jako u zadaný případ, ale metoda má jiný podpis, protože odkazuje na **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Instanci můžete také odstranit přímo pomocí jejího ID:

```java
mToDoTable.delete(ID);
```

### <a name="return-all-rows-from-an-untyped-table"></a><a name="json_get"></a>Vrátit všechny řádky z netypové tabulky
Následující kód ukazuje, jak načíst celou tabulku. Vzhledem k tomu, že používáte tabulku JSON, můžete selektivně načíst pouze některé sloupce tabulky.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

Pro netypový model je k dispozici stejná sada metod filtrování, filtrování a stránkování, které jsou k dispozici pro typový model.

## <a name="implement-offline-sync"></a><a name="offline-sync"></a>Implementace offline synchronizace

Sada Azure Mobile Apps Client SDK také implementuje offline synchronizaci dat pomocí databáze SQLite k místnímu ukládání kopie dat serveru.  Operace prováděné v offline tabulce nevyžadují mobilní připojení k práci.  Offline synchronizace pomáhá v odolnosti a výkonu na úkor složitější logiky pro řešení konfliktů.  Sada Azure Mobile Apps Client SDK implementuje následující funkce:

* Přírůstková synchronizace: Stahují se pouze aktualizované a nové záznamy, což šetří šířku pásma a spotřebu paměti.
* Optimistická souběžnost: Předpokládá se, že operace budou úspěšné.  Řešení konfliktů je odloženo, dokud nebudou na serveru provedeny aktualizace.
* Řešení konfliktů: Sada SDK zjistí, kdy byla na serveru provedena konfliktní změna, a poskytuje háčky pro upozornění uživatele.
* Obnovitelné odstranění: Odstraněné záznamy jsou označeny jako odstraněné, což ostatním zařízením umožňuje aktualizovat jejich offline mezipaměť.

### <a name="initialize-offline-sync"></a>Inicializovat offline synchronizaci

Každá offline tabulka musí být před použitím definována v mezipaměti offline.  Definice tabulky se obvykle provádí ihned po vytvoření klienta:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Získání odkazu na tabulku mezipaměti offline

Pro online tabulku používáte `.getTable()`.  Pro offline tabulku `.getSyncTable()`použijte :

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Všechny metody, které jsou k dispozici pro online tabulky (včetně filtrování, řazení, stránkování, vkládání dat, aktualizace dat a odstranění dat) fungují stejně dobře v online i offline tabulkách.

### <a name="synchronize-the-local-offline-cache"></a>Synchronizace místní mezipaměti offline

Synchronizace je pod kontrolou vaší aplikace.  Zde je příklad metody synchronizace:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Pokud je k metodě `.pull(query, queryname)` poskytnut název dotazu, pak přírůstková synchronizace se používá k vrácení pouze záznamů, které byly vytvořeny nebo změněny od posledního úspěšně dokončeného vyžádat.

### <a name="handle-conflicts-during-offline-synchronization"></a>Zpracovat konflikty během offline synchronizace

Pokud dojde ke `.push()` konfliktu během `MobileServiceConflictException` operace, je vyvolána.   Položka vydaná serverem je vložena do výjimky a může být načtena `.getItem()` na výjimce.  Upravte nabízenou položku voláním následujících položek v objektu MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Jakmile jsou všechny konflikty označeny `.push()` podle potřeby, zavolejte znovu, abyste vyřešili všechny konflikty.

## <a name="call-a-custom-api"></a><a name="custom-api"></a>Volání vlastního rozhraní API

Vlastní rozhraní API umožňuje definovat vlastní koncové body, které zveřejňují funkce serveru, které nejsou mapovány na operaci vložení, aktualizace, odstranění nebo čtení. Pomocí vlastního rozhraní API můžete mít větší kontrolu nad zasílání ms, včetně čtení a nastavení záhlaví zpráv HTTP a definování formátu textu zprávy než JSON.

Z klienta Android zavoláte metodu **invokeApi** a zavoláte vlastní koncový bod rozhraní API. Následující příklad ukazuje, jak volat koncový bod rozhraní API s názvem **completeAll**, který vrací třídu kolekce s názvem **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

Metoda **invokeApi** je volána na straně klienta, který odešle požadavek POST do nového vlastního rozhraní API. Výsledek vrácený vlastním rozhraním API se zobrazí v dialogovém okně zpráv, stejně jako všechny chyby. Jiné verze **invokeApi** umožňují volitelně odeslat objekt v těle požadavku, zadat metodu HTTP a odeslat parametry dotazu s požadavkem. Netypové verze **invokeApi** jsou k dispozici také.

## <a name="add-authentication-to-your-app"></a><a name="authentication"></a>Přidání ověřování do aplikace

Návody již podrobně popisují, jak tyto funkce přidat.

Služba App Service podporuje [ověřování uživatelů aplikací](app-service-mobile-android-get-started-users.md) pomocí různých poskytovatelů externích identit: Facebook, Google, účet Microsoft, Twitter a Azure Active Directory. Můžete nastavit oprávnění pro tabulky, která omezí přístup pro určité operace pouze na ověřené uživatele. Identitu ověřených uživatelů můžete také použít k implementaci autorizačních pravidel v back-endu.

Podporovány jsou dva toky ověřování: tok **serveru** a tok **klienta.** Tok serveru poskytuje nejjednodušší ověřování, protože závisí na webovérozhraní zprostředkovatelů identity.  K implementaci ověřování toku serveru nejsou potřeba žádné další sady SDK. Ověřování toku serveru neposkytuje hlubokou integraci do mobilního zařízení a je doporučeno pouze pro ověření scénářů konceptu.

Tok klienta umožňuje hlubší integraci s funkcemi specifickými pro zařízení, jako je jednotné přihlašování, protože závisí na sadách SDK poskytovaných zprostředkovatelem identity.  Můžete například integrovat facebookovou sadku SDK do mobilní aplikace.  Mobilní klient se přepne do aplikace Facebook a potvrdí vaše přihlášení před výměnou zpět do mobilní aplikace.

K povolení ověřování v aplikaci jsou potřeba čtyři kroky:

* Zaregistrujte aplikaci pro ověřování u poskytovatele identity.
* Nakonfigurujte back-end služby App Service.
* Omezte oprávnění k tabulce na ověřené uživatele pouze v back-endu služby App Service.
* Přidejte do aplikace ověřovací kód.

Můžete nastavit oprávnění pro tabulky, která omezí přístup pro určité operace pouze na ověřené uživatele. Můžete také použít SID ověřeného uživatele k úpravě požadavků.  Další informace naleznete [v části Začínáme s ověřováním] a v dokumentaci k souboru HOWTO sady Server SDK.

### <a name="authentication-server-flow"></a><a name="caching"></a>Ověřování: Tok serveru

Následující kód spustí proces přihlášení toku serveru pomocí poskytovatele Google.  Další konfigurace je vyžadována z důvodu požadavků na zabezpečení poskytovatele Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Kromě toho přidejte do hlavní třídy Activity následující metodu:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

Definované `GOOGLE_LOGIN_REQUEST_CODE` v hlavní Aktivita se `login()` používá pro `onActivityResult()` metodu a v rámci metody.  Můžete zvolit libovolné jedinečné číslo, pokud je v `login()` rámci metody `onActivityResult()` a metody použito stejné číslo.  Pokud abstrahujete klientský kód do servisního adaptéru (jak je znázorněno výše), měli byste zavolat příslušné metody na servisním adaptéru.

Je také nutné nakonfigurovat projekt pro vlastní karty.  Nejprve zadejte adresu URL přesměrování.  Do doplňku přidejte `AndroidManifest.xml`následující úryvek :

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Přidejte **redirectUriScheme** `build.gradle` do souboru pro vaši aplikaci:

```gradle
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Nakonec přidejte `com.android.support:customtabs:28.0.0` do seznamu závislostí v souboru: `build.gradle`

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Získat ID přihlášeného uživatele od **MobileServiceUser** pomocí **metody getUserId.** Příklad použití futures k volání asynchronních přihlašovacích api naleznete v [tématu Začínáme s ověřováním].

> [!WARNING]
> Uvedené schéma adres URL rozlišuje malá a velká písmena.  Ujistěte se, `{url_scheme_of_you_app}` že všechny výskyty případu shody.

### <a name="cache-authentication-tokens"></a><a name="caching"></a>Ověřovací tokeny mezipaměti

Ukládání ověřovacích tokenů do mezipaměti vyžaduje, abyste v zařízení ukládali ID uživatele a ověřovací token místně. Při příštím spuštění aplikace zkontrolujte mezipaměť a pokud jsou tyto hodnoty k dispozici, můžete přeskočit postup přihlášení a rehydratovat klienta s těmito daty. Tato data jsou však citlivá a měla by být uložena šifrovaná pro bezpečnost v případě, že telefon bude ukraden.  Úplný příklad ukládání ověřovacích tokenů do mezipaměti můžete zobrazit v [části Tokeny ověřování mezipaměti][7].

Při pokusu o použití tokenu s prošlou platností obdržíte *neoprávněnou odpověď 401.* Chyby ověřování můžete zpracovávat pomocí filtrů.  Filtruje zachytit požadavky na back-end služby App Service. Kód filtru testuje odpověď pro 401, aktivuje proces přihlášení a potom obnoví požadavek, který vygeneroval 401.

### <a name="use-refresh-tokens"></a><a name="refresh"></a>Použít aktualizační tokeny

Token vrácený ověřováním a autorizací služby Azure App Service má definovanou životnost jedné hodiny.  Po uplynutí této doby je nutné znovu ověřit uživatele.  Pokud používáte dlouhodobý token, který jste obdrželi prostřednictvím ověřování toku klienta, můžete se znovu ověřit pomocí ověřování a autorizace služby Azure App Service pomocí stejného tokenu.  Další token služby Azure App Service se generuje s novou životností.

Můžete také zaregistrovat zprostředkovatele používat obnovovací tokeny.  Obnovovací token není vždy k dispozici.  Je vyžadována další konfigurace:

* Pro **službu Azure Active Directory**nakonfigurujte tajný klíč klienta pro aplikaci Azure Active Directory.  Při konfiguraci ověřování Azure Active Directory zadejte tajný klíč klienta ve službě Azure App Service.  Při `.login()`volání `response_type=code id_token` , pass jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Pro **Google**, `access_type=offline` pass jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* V **případě účtu** `wl.offline_access` Microsoft vyberte obor.

Chcete-li aktualizovat `.refreshUser()`token, volejte :

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Jako osvědčený postup vytvořte filtr, který detekuje odpověď 401 ze serveru a pokusí se aktualizovat token uživatele.

## <a name="log-in-with-client-flow-authentication"></a>Přihlášení pomocí ověřování toku klienta

Obecný proces pro přihlášení pomocí ověřování toku klienta je následující:

* Nakonfigurujte ověřování a autorizaci služby Azure App Service stejně jako ověřování toku serveru.
* Integrujte ověřovací ho zprostředkovatele ověřování sady SDK pro ověřování a vytvářejte přístupový token.
* Volání `.login()` metody takto`result` ( by `AuthenticationResult`měla být ):

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Podívejte se na příklad úplného kódu v další části.

Nahraďte metodu `onSuccess()` jakýmkoli kódem, který chcete použít při úspěšném přihlášení.  Řetězec `{provider}` je platný poskytovatel: **aad** (Azure Active Directory), **Facebook**, **Google**, **MicrosoftAccount**, nebo **twitter**.  Pokud jste implementovali vlastní ověřování, můžete také použít vlastní značku zprostředkovatele ověřování.

### <a name="authenticate-users-with-the-active-directory-authentication-library-adal"></a><a name="adal"></a>Ověření uživatelů pomocí knihovny ověřování služby Active Directory (ADAL)

Pomocí knihovny ADAL (Active Directory Authentication Library) můžete uživatele přihlásit do aplikace pomocí služby Azure Active Directory. Použití přihlášení toku klienta je `loginAsync()` často vhodnější než použití metod, protože poskytuje více nativní ux pocit a umožňuje další přizpůsobení.

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení do Služby AAD podle kurzu [Jak nakonfigurovat službu App Service pro přihlášení do služby Active Directory.][22] Ujistěte se, že dokončíte volitelný krok registrace nativní klientské aplikace.
2. Nainstalujte ADAL úpravou souboru build.gradle tak, aby obsahoval následující definice:

    ```gradle
    repositories {
        mavenCentral()
        flatDir {
            dirs 'libs'
        }
        maven {
            url "YourLocalMavenRepoPath\\.m2\\repository"
        }
    }
    packagingOptions {
        exclude 'META-INF/MSFTSIG.RSA'
        exclude 'META-INF/MSFTSIG.SF'
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. Přidejte do aplikace následující kód, který provádí následující náhrady:

    * Nahraďte **INSERT-AUTHORITY-HERE** názvem klienta, ve kterém jste zřídit svou aplikaci. Formát by https://login.microsoftonline.com/contoso.onmicrosoft.comměl být .
    * Nahraďte **INSERT-RESOURCE-ID-ZDE** ID klienta pro back-end mobilní aplikace. ID klienta můžete získat na kartě **Upřesnit** v části **Nastavení služby Azure Active Directory** na portálu.
    * Nahraďte **INSERT-CLIENT-ID-ZDE** ID klienta, které jste zkopírovali z nativní klientské aplikace.
    * Nahraďte **rozhraní INSERT-REDIRECT-URI-ZDE** koncovým bodem vašeho webu */.auth/login/done* pomocí schématu HTTPS. Tato hodnota by *https://contoso.azurewebsites.net/.auth/login/done*měla být podobná .

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="adjust-the-client-server-communication"></a><a name="filters"></a>Úprava komunikace mezi klientem a serverem

Klient připojení je obvykle základní připojení HTTP pomocí podkladové knihovny HTTP dodávané s Android SDK.  Existuje několik důvodů, proč byste to chtěli změnit:

* Chcete upravit časové prostranštiny pomocí alternativní knihovny HTTP.
* Chcete poskytnout indikátor průběhu.
* Chcete přidat vlastní záhlaví pro podporu funkce správy rozhraní API.
* Chcete zachytit neúspěšnou odpověď, abyste mohli implementovat opětovné ověření.
* Chcete protokolovat požadavky na back-end do analytické služby.

### <a name="using-an-alternate-http-library"></a>Použití alternativní knihovny HTTP

Volání `.setAndroidHttpClientFactory()` metody ihned po vytvoření odkazu klienta.  Chcete-li například nastavit časový limit připojení na 60 sekund (namísto výchozích 10 sekund):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementace filtru průběhu

Můžete implementovat zachytit každý požadavek `ServiceFilter`implementací .  Například následující aktualizace předem vytvořené indikátor průběhu:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Tento filtr můžete ke klientovi připojit následujícím způsobem:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Přizpůsobit záhlaví požadavků

Použijte následující `ServiceFilter` a připevněte filtr stejným `ProgressFilter`způsobem jako :

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="configure-automatic-serialization"></a><a name="conversions"></a>Konfigurace automatické serializace

Můžete zadat strategii převodu, která se vztahuje na každý sloupec pomocí [gson][3] API. Klientská knihovna Android používá [gson][3] na pozadí serializovat objekty Java do dat JSON před odesláním dat do služby Azure App Service.  Následující kód používá **metodu setFieldNamingStrategy()** k nastavení strategie. Tento příklad odstraní počáteční znak ("m") a pak malá písmena další znak pro každý název pole. Například by se změní "mId" na "id."  Implementujte strategii převodu, `SerializedName()` abyste snížili potřebu poznámky na většině polí.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStrategy)
);
```

Tento kód musí být proveden před vytvořením odkazu mobilního klienta pomocí klienta **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Začínáme s ověřováním]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
