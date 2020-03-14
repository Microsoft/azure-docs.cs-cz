---
title: Jak používat sadu SDK pro Android
description: Jak používat sadu Azure Mobile Apps SDK pro Android
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 52e91d900ce0f22862904695ba8adf463219c469
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249383"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Jak používat sadu Azure Mobile Apps SDK pro Android

V této příručce se dozvíte, jak používat klientskou sadu SDK pro Android pro Mobile Apps k implementaci běžných scénářů, například:

* Dotazování na data (vložení, aktualizace a odstranění).
* Ověřování.
* Zpracování chyb.
* Přizpůsobení klienta.

Tato příručka se zaměřuje na Android SDK na straně klienta.  Další informace o sadách SDK na straně serveru pro Mobile Apps najdete v tématu [Working with .NET back-end SDK][10] nebo [Jak používat back-end SDK Node. js][11].

## <a name="reference-documentation"></a>Referenční dokumentace

[Referenční informace k rozhraní Javadocs API][12] pro klientskou knihovnu Android najdete na GitHubu.

## <a name="supported-platforms"></a>Podporované platformy

Sada Azure Mobile Apps SDK pro Android podporuje rozhraní API úrovně 19 až 24 (KitKat až nougat) pro faktory pro telefon a tabletový formulář.  Ověřování zejména využívá běžný přístup k webovému rozhraní pro shromažďování přihlašovacích údajů.  Ověřování pomocí toku serveru nefunguje u malých formulářových zařízení, jako jsou například kukátka.

## <a name="setup-and-prerequisites"></a>Nastavení a předpoklady

Dokončete kurz pro [rychlý start Mobile Apps](app-service-mobile-android-get-started.md) .  Tato úloha zajišťuje splnění všech předpokladů pro vývoj Mobile Apps Azure.  Rychlý Start vám také pomůže nakonfigurovat svůj účet a vytvořit první back-end mobilní aplikace.

Pokud se rozhodnete nedokončit rychlý úvodní kurz, proveďte následující úlohy:

* [vytvořte back-end mobilní aplikace][13] pro použití s vaší aplikací pro Android.
* V Android Studio [aktualizujte soubory sestavení Gradle](#gradle-build).
* [Povolit internetové oprávnění](#enable-internet).

### <a name="gradle-build"></a>Aktualizace souboru buildu Gradle

Změňte oba soubory **Build. Gradle** :

1. Přidejte tento kód do souboru **Build. Gradle** na úrovni *projektu* :

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

2. Přidejte tento kód do souboru **Build. Gradle** na úrovni *aplikace modulu* v rámci značky *závislosti* :

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    V současné době je nejnovější verze 3.4.0. Podporované verze jsou uvedené [na bintray][14].

### <a name="enable-internet"></a>Povolit internetové oprávnění

Aby bylo možné získat přístup k Azure, musí mít aplikace povolené oprávnění pro INTERNET. Pokud ještě není povolené, přidejte do souboru **souboru AndroidManifest. XML** následující řádek kódu:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Vytvoření připojení klienta

Azure Mobile Apps poskytuje mobilní aplikaci čtyři funkce:

* Přístup k datům a offline synchronizace pomocí služby Azure Mobile Apps.
* Zavolejte vlastní rozhraní API vytvořená pomocí sady Azure Mobile Apps Server SDK.
* Ověřování pomocí Azure App Service ověřování a autorizace.
* Registrace nabízených oznámení pomocí Notification Hubs

Každá z těchto funkcí nejprve vyžaduje, abyste vytvořili objekt `MobileServiceClient`.  V mobilním klientovi by se měl vytvořit jenom jeden objekt `MobileServiceClient` (to znamená, že by měl být vzor s jedním typem).  Vytvoření objektu `MobileServiceClient`:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` je buď řetězec, nebo objekt URL, který odkazuje na váš mobilní back-end.  Pokud používáte Azure App Service k hostování mobilního back-endu, ujistěte se, že používáte zabezpečenou `https://` verzi adresy URL.

Klient také vyžaduje přístup k aktivitě nebo kontextu – parametr `this` v příkladu.  Konstrukce MobileServiceClient by měla probíhat v rámci metody `onCreate()` aktivity, na kterou se odkazuje `AndroidManifest.xml` souboru.

V rámci osvědčeného postupu byste měli v rámci své vlastní třídy (singleton-vzoru) vytvořit abstraktní komunikaci serveru.  V takovém případě byste měli předat aktivitu v rámci konstruktoru a patřičně tak nakonfigurovat službu.  Příklad:

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

Nyní můžete volat `AzureServiceAdapter.Initialize(this);` v metodě `onCreate()` hlavní aktivity.  Jakékoli jiné metody, které potřebují přístup k klientovi, používají `AzureServiceAdapter.getInstance();` k získání odkazu na adaptér služby.

## <a name="data-operations"></a>Datové operace

Základem sady Azure Mobile Apps SDK je poskytnout přístup k datům uloženým v SQL Azure back-endu mobilní aplikace.  K těmto datům můžete přistupovat pomocí tříd silného typu (preferované) nebo netypových dotazů (nedoporučujeme).  Hromadná část této části se zabývá používáním tříd silného typu.

### <a name="define-client-data-classes"></a>Definovat klientské třídy dat

Pokud chcete získat přístup k datům z SQL Azure tabulek, definujte klientské třídy dat, které odpovídají tabulkám v back-endu mobilní aplikace. Příklady v tomto tématu předpokládají tabulku s názvem **myDataTable**, která má následující sloupce:

* id
* text
* Plňte

Odpovídající typový objekt na straně klienta se nachází v souboru s názvem **myDataTable. Java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Přidejte metody getter a setter pro každé pole, které přidáte.  Pokud vaše SQL Azure tabulka obsahuje více sloupců, přidejte do této třídy odpovídající pole.  Například pokud má DTO (objekt pro přenos dat) sloupec priority s celými čísly, můžete přidat toto pole spolu s jeho metodami getter a setter:

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

Další informace o tom, jak v Mobile Apps back-endu vytvořit další tabulky, najdete v tématech [How to: define a Table Controller][15] (.NET back-end) nebo [define Tables pomocí dynamického schématu][16] (back-end Node. js).

Tabulka back-endu Azure Mobile Apps definuje pět speciálních polí, z nichž čtyři jsou k dispozici pro klienty:

* `String id`: globálně jedinečné ID záznamu.  V souladu s osvědčeným postupem nastavte ID řetězcové vyjádření objektu [UUID][17] .
* `DateTimeOffset updatedAt`: datum a čas poslední aktualizace.  Pole updatedAt je nastaveno serverem a nikdy by nemělo být nastaveno vaším klientským kódem.
* `DateTimeOffset createdAt`: datum a čas vytvoření objektu.  Pole createdAt je nastaveno serverem a nikdy by nemělo být nastaveno vaším klientským kódem.
* `byte[] version`: obvykle reprezentované jako řetězec, verze je také nastavena serverem.
* `boolean deleted`: označuje, že záznam byl odstraněn, ale ještě nebyl smazán.  Nepoužívejte `deleted` jako vlastnost ve třídě.

Pole `id` je povinné.  Pole `updatedAt` a `version` pole se používají pro offline synchronizaci (pro přírůstkovou synchronizaci a řešení konfliktů).  Pole `createdAt` je referenční pole a klient ho nepoužívá.  Názvy jsou "mezidrátové" názvy vlastností a nelze je upravit.  Pomocí knihovny [gson][3] ale můžete vytvořit mapování mezi vaším objektem a názvy "napříč" vodiči.  Příklad:

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

Chcete-li získat přístup k tabulce, vytvořte nejprve objekt [MobileServiceTable][8] voláním metody **GetTable** na [MobileServiceClient][9].  Tato metoda má dvě přetížení:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

V následujícím kódu je **mClient** odkazem na váš objekt MobileServiceClient.  První přetížení se používá tam, kde název třídy a název tabulky jsou stejné a je ta, která se používá v rychlém startu:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Druhé přetížení se použije, když se název tabulky liší od názvu třídy: první parametr je název tabulky.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Dotaz na back-end tabulku

Nejprve získejte odkaz na tabulku.  Pak spusťte dotaz na odkaz na tabulku.  Dotaz je libovolná kombinace:

* [Klauzule filtru](#filtering)`.where()`.
* [Klauzule řazení](#sorting)`.orderBy()`.
* [Klauzule výběru pole](#selection)`.select()`.
* Pro [stránkované výsledky](#paging)`.skip()` a `.top()`.

Klauzule musí být uvedeny v předchozím pořadí.

### <a name="filter"></a>Filtrování výsledků

Obecná forma dotazu je:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Předchozí příklad vrátí všechny výsledky (až do maximální velikosti stránky nastavené serverem).  Metoda `.execute()` spustí dotaz na back-end.  Dotaz se před přenosem do back-endu Mobile Apps převede na dotaz [OData V3][19] .  Po přijetí Mobile Apps back-end převede dotaz na příkaz SQL před jeho spuštěním na instanci SQL Azure.  Vzhledem k tomu, že síťová aktivita nějakou dobu trvá, metoda `.execute()` vrátí [`ListenableFuture<E>`][18].

### <a name="filtering"></a>Filtrovat vrácená data

Následující spuštění dotazu vrátí všechny položky z tabulky **ToDoItem** , kde **kompletní** **hodnota je false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** je odkaz na tabulku mobilní služby, kterou jsme vytvořili dříve.

Definujte filtr pomocí volání metody **WHERE** v odkazu na tabulku. Metoda **WHERE** následovaná metodou **pole** následovaný metodou, která určuje logický predikát. Mezi možné metody predikátu patří **EQ** (EQUAL), **ne** (není rovno), **gt** (větší než), **GE** (větší než nebo rovno), **lt** (menší než), **Le** (je menší než nebo rovno). Tyto metody umožňují porovnat čísla a řetězcová pole s konkrétními hodnotami.

Můžete filtrovat data. Následující metody umožňují porovnat celé pole data nebo části data: **rok**, **měsíc**, **den**, **hodina**, **minuta**a **sekunda**. Následující příklad přidá filtr pro položky, jejichž *Datum splatnosti* se rovná 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Následující metody podporují složité filtry na pole řetězců: **StartsWith**, **endsWith**, **Concat**, **substring**, **IndexOf**, **Replace**, **toLower**, **toUpper**, **Trim**a **Length**. Následující příklad filtruje řádky tabulky, kde sloupec *text* začíná řetězcem "PRI0".

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Následující metody operátoru jsou podporovány v polích číslo: **Add**, **Sub**, **mul**, **div**, **mod**,mod, **ZAOKR**. a **Round**. Následující příklad filtruje řádky tabulky, jejichž **Doba trvání** je sudé číslo.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Predikáty lze kombinovat pomocí těchto logických metod: **a**, **nebo** . Následující příklad kombinuje dva z předchozích příkladů.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Seskupení a vnořování logických operátorů:

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

Podrobnější diskuzi a příklady filtrování najdete v tématu [prozkoumávání bohatosti modelu dotazů klientů pro Android][20].

### <a name="sorting"></a>Řazení vrácených dat

Následující kód vrátí všechny položky z tabulky **ToDoItems** seřazené vzestupně podle *textového* pole. *mToDoTable* je odkaz na tabulku back-end, kterou jste vytvořili dříve:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

První parametr metody **OrderBy** je řetězec, který se rovná názvu pole, podle kterého se má řadit. Druhý parametr používá výčet **QueryOrder** k určení, zda se má seřadit vzestupně nebo sestupně.  Pokud filtrujete pomocí metody ***WHERE*** , metoda ***WHERE*** musí být vyvolána před metodou ***OrderBy*** .

### <a name="selection"></a>Vybrat konkrétní sloupce

Následující kód ilustruje, jak vrátit všechny položky z tabulky **ToDoItems**, ale zobrazuje pouze pole **Complete** a **text** . **mToDoTable** je odkaz na tabulku back-end, kterou jsme vytvořili dříve.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametry funkce Select jsou názvy řetězců sloupců tabulky, které chcete vrátit.  Metoda **Select** musí následovat za metodami, jako je například **WHERE** a **OrderBy**. Může následovat metody stránkování, jako je **Skip** a **Top**.

### <a name="paging"></a>Vrátit data na stránkách

Data se **vždycky** vrátí na stránkách.  Maximální počet vrácených záznamů je nastaven serverem.  Pokud klient požaduje více záznamů, vrátí server maximální počet záznamů.  Ve výchozím nastavení je maximální velikost stránky na serveru 50 záznamů.

První příklad ukazuje, jak vybrat horní pět položek z tabulky. Dotaz vrátí položky z tabulky **ToDoItems**. **mToDoTable** je odkaz na tabulku back-end, kterou jste vytvořili dříve:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Zde je dotaz, který přeskočí prvních pět položek a potom vrátí další pět:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Pokud chcete získat všechny záznamy v tabulce, implementujte kód pro iteraci na všech stránkách:

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

Požadavek na všechny záznamy pomocí této metody vytvoří minimálně dva požadavky na Mobile Apps back-end.

> [!TIP]
> Zvolíte-li velikost pravé stránky, bude zůstatek mezi využitím paměti během žádosti, využití šířky pásma a zpoždění při úplném příjmu dat.  Výchozí (záznamy 50) jsou vhodné pro všechna zařízení.  Pokud pracujete výhradně na větších paměťových zařízeních, zvyšte až 500.  Zjistili jsme, že zvětšení velikosti stránky přesahuje 500 záznamů má za následek nepřijatelné zpoždění a velké množství problémů s velkou pamětí.

### <a name="chaining"></a>Postupy: zřetězení metod dotazů

Metody používané při dotazování na back-end tabulky můžou být zřetězené. Řetězení metod dotazů umožňuje vybrat konkrétní sloupce filtrovaných řádků, které jsou seřazené a stránkované. Můžete vytvářet složité logické filtry.  Každá metoda dotazu vrátí objekt dotazu. Chcete-li ukončit sérii metod a ve skutečnosti spustit dotaz, zavolejte metodu **Execute** . Příklad:

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

Metody zřetězeného dotazu musí být seřazeny takto:

1. Filtrování (**WHERE**) metod.
2. Metody řazení (**OrderBy**).
3. Výběr (**Výběr**) metod.
4. metody stránkování (**Skip** a **Top**).

## <a name="binding"></a>Svázání dat s uživatelským rozhraním

Datová vazba zahrnuje tři komponenty:

* Zdroj dat
* Rozložení obrazovky
* Adaptér, který spojuje oba dva.

V našem ukázkovém kódu vrátíme data z Mobile Apps SQL Azure tabulce **ToDoItem** do pole. Tato aktivita je běžným vzorem pro datové aplikace.  Databázové dotazy často vracejí kolekci řádků, které klient získá v seznamu nebo poli. V této ukázce je pole zdrojem dat.  Kód určuje rozložení obrazovky, které definuje zobrazení dat zobrazených na zařízení.  Tyto dvě jsou vázány spolu s adaptérem, který v tomto kódu je rozšířením třídy **ArrayAdapter&lt;ToDoItem&gt;** .

#### <a name="layout"></a>Definovat rozložení

Rozložení je definováno několika fragmenty kódu XML. S ohledem na existující rozložení představuje následující kód **ListView** , který chceme naplnit daty serveru.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

V předchozím kódu atribut *ListItem* Určuje ID rozložení pro jednotlivé řádky v seznamu. Tento kód určuje zaškrtávací políčko a jeho přidružený text a získá pro každou položku v seznamu instanci. Toto rozložení nezobrazuje pole **ID** a složitější rozložení by určovalo další pole v zobrazení. Tento kód je v souboru **row_list_to_do. XML** .

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

#### <a name="adapter"></a>Definování adaptéru
Vzhledem k tomu, že zdrojem dat našeho zobrazení je pole **ToDoItem**, podtřídou našeho adaptéru z třídy **ArrayAdapter&lt;ToDoItem&gt;** . Tato podtřída vytvoří zobrazení pro každou **ToDoItem** pomocí rozložení **row_list_to_do** .  V našem kódu definujeme následující třídu, která je rozšířením třídy **ArrayAdapter&lt;E&gt;** :

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Přepište metodu **GetView** adaptéru. Příklad:

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

V naší aktivitě vytvoříme instanci této třídy následujícím způsobem:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Druhým parametrem konstruktoru ToDoItemAdapter je odkaz na rozložení. Nyní můžeme vytvořit instanci **ListView** a přiřadit adaptér k **objektu ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Vytvoření vazby k uživatelskému rozhraní pomocí adaptéru

Nyní jste připraveni použít datovou vazbu. Následující kód ukazuje, jak získat položky v tabulce a vyplní místní adaptér položkami vrácenými položkami.

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

Zavolejte adaptér, kdykoli upravíte tabulku **ToDoItem** . Vzhledem k tomu, že se změny provádějí na záznamu podle záznamu, místo kolekce se zpracuje jeden řádek. Když vložíte položku, zavolejte na adaptér metodu **Add** ; Při odstraňování volejte metodu **Remove** .

Úplný příklad najdete v [projektu pro rychlý Start pro Android][21].

## <a name="inserting"></a>Vložení dat do back-endu

Vytvořte instanci instance třídy *ToDoItem* a nastavte její vlastnosti.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Pak použijte **Insert ()** pro vložení objektu:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Vrácená entita odpovídá datům vloženým do tabulky back-endu, včetně ID a dalších hodnot (například `createdAt`, `updatedAt`a `version` polí) nastavených v back-endu.

Mobile Apps tabulky vyžadují sloupec primárního klíče s názvem **ID**. Tento sloupec musí být řetězec. Výchozí hodnota sloupce ID je identifikátor GUID.  Můžete zadat jiné jedinečné hodnoty, například e-mailové adresy nebo uživatelská jména. Pokud pro vložený záznam není zadána hodnota ID řetězce, back-end vygeneruje nový identifikátor GUID.

Hodnoty ID řetězce poskytují následující výhody:

* ID lze generovat bez odeslání odezvy do databáze.
* Záznamy je snazší sloučit z různých tabulek nebo databází.
* Hodnoty ID se integrují lépe díky logice aplikace.

Pro podporu offline synchronizace jsou **vyžadovány** hodnoty ID řetězce.  ID nelze změnit, jakmile je uloženo v back-end databázi.

## <a name="updating"></a>Aktualizace dat v mobilní aplikaci

Chcete-li aktualizovat data v tabulce, předejte nový objekt metodě **Update ()** .

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

V tomto příkladu je *položka* odkazem na řádek v tabulce *ToDoItem* , ve kterém se udělaly nějaké změny.  Řádek se stejným **ID** se aktualizuje.

## <a name="deleting"></a>Odstranění dat v mobilní aplikaci

Následující kód ukazuje, jak odstranit data z tabulky zadáním datového objektu.

```java
mToDoTable
    .delete(item);
```

Položku můžete také odstranit zadáním pole **ID** řádku, který chcete odstranit.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Vyhledat konkrétní položku podle ID

Vyhledat položku s konkrétním polem **ID** pomocí metody **lookUp ()** :

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Postupy: práce s netypovými daty

Netypový programovací model poskytuje přesnou kontrolu pomocí serializace JSON.  Existují některé běžné scénáře, kde můžete chtít použít Netypový programovací model. Pokud například tabulka back-endu obsahuje mnoho sloupců a stačí pouze odkazovat na podmnožinu sloupců.  Typový model vyžaduje, abyste v datové třídě definovali všechny sloupce definované v Mobile Apps back-endu.  Většina volání rozhraní API pro přístup k datům je podobná typovým voláním programování. Hlavní rozdíl je v tom, že v netypovém modelu vyvoláte metody objektu **MobileServiceJsonTable** namísto objektu **MobileServiceTable** .

### <a name="json_instance"></a>Vytvoření instance netypové tabulky

Podobně jako u typového modelu začínáte získáním odkazu na tabulku, ale v tomto případě se jedná o objekt **MobileServicesJsonTable** . Získejte odkaz voláním metody **GetTable** v instanci klienta:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Po vytvoření instance **MobileServiceJsonTable**má prakticky stejné rozhraní API jako s typovým programovacím modelem. V některých případech metody přebírají Netypový parametr namísto typovaného parametru.

### <a name="json_insert"></a>Vložit do netypové tabulky
Následující kód ukazuje, jak provést vložení. Prvním krokem je vytvoření [JsonObject][1], který je součástí knihovny [gson][3] .

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Pak použijte **Insert ()** pro vložení netypového objektu do tabulky.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Pokud potřebujete získat ID vloženého objektu, použijte metodu **getAsJsonPrimitive ()** .

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Odstranit z netypové tabulky
Následující kód ukazuje, jak odstranit instanci, v tomto případě stejnou instanci **JsonObject** , která byla vytvořena v předchozím příkladu *vložení* . Kód je stejný jako u typového případu, ale metoda má jiný podpis, protože odkazuje na **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Instanci můžete také odstranit přímo pomocí jejího ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Vrátí všechny řádky z netypové tabulky.
Následující kód ukazuje, jak načíst celou tabulku. Vzhledem k tomu, že používáte tabulku JSON, můžete selektivně načíst jenom některé sloupce tabulky.

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

Pro Netypový model jsou k dispozici stejné sady metod filtrování, filtrování a stránkování, které jsou k dispozici pro typový model.

## <a name="offline-sync"></a>Implementace offline synchronizace

Klientská sada SDK pro Azure Mobile Apps také implementuje offline synchronizaci dat pomocí databáze SQLite k uložení kopie dat serveru místně.  Operace prováděné v offline tabulce nevyžadují, aby mobilní připojení fungovalo.  Offline synchronizace pomáhá zajistit odolnost a výkon na úkor složitější logiky při řešení konfliktů.  Klientská sada SDK pro Azure Mobile Apps implementuje následující funkce:

* Přírůstková synchronizace: byly staženy pouze aktualizované a nové záznamy, úspora šířky pásma a paměti.
* Optimistická souběžnost: operace se považují za úspěšné.  Řešení konfliktů je odloženo, dokud se na serveru neprovede aktualizace.
* Řešení konfliktů: sada SDK detekuje, kdy byla na serveru provedena kolidující změna, a poskytuje uživatelům upozornění.
* Obnovitelné odstranění: odstraněné záznamy jsou označené jako odstraněné a povolují ostatním zařízením aktualizovat jejich offline mezipaměť.

### <a name="initialize-offline-sync"></a>Inicializovat offline synchronizaci

Před použitím musí být každá offline tabulka definovaná v offline mezipaměti.  Normálně se definice tabulky provádí hned po vytvoření klienta:

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

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Získání odkazu na tabulku offline mezipaměti

V případě online tabulky používáte `.getTable()`.  Pro offline tabulku použijte `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Všechny metody, které jsou k dispozici pro online tabulky (včetně filtrování, řazení, stránkování, vkládání dat, aktualizace dat a odstraňování dat) fungují stejně dobře v online a offline tabulkách.

### <a name="synchronize-the-local-offline-cache"></a>Synchronizace místní offline mezipaměti

Synchronizace je v rámci řízení vaší aplikace.  Tady je příklad metody synchronizace:

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

Pokud je pro metodu `.pull(query, queryname)` k dispozici název dotazu, pak se přírůstková synchronizace používá k vrácení pouze záznamů, které byly vytvořeny nebo změněny od posledního úspěšného přijetí operace Pull.

### <a name="handle-conflicts-during-offline-synchronization"></a>Zpracování konfliktů při offline synchronizaci

Pokud dojde ke konfliktu během operace `.push()`, je vyvolána `MobileServiceConflictException`.   Položka vydaná serverem je vložena do výjimky a může být načtena `.getItem()`em na výjimku.  Upravte nabízený zápis voláním následujících položek objektu MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Jakmile budou všechny konflikty označeny jako požadované, zavolejte `.push()` znovu pro vyřešení všech konfliktů.

## <a name="custom-api"></a>Volání vlastního rozhraní API

Vlastní rozhraní API umožňuje definovat vlastní koncové body, které zveřejňují funkci serveru, která není namapována na operaci vložení, aktualizace, odstranění nebo čtení. Pomocí vlastního rozhraní API můžete mít větší kontrolu nad zasíláním zpráv, včetně čtení a nastavování hlaviček zpráv HTTP a definování formátu textu zprávy, který je jiný než JSON.

Z klienta pro Android zavoláte metodu **invokeApi** , která bude volat vlastní koncový bod rozhraní API. Následující příklad ukazuje, jak volat koncový bod rozhraní API s názvem **completeAll**, který vrací třídu kolekce s názvem **MarkAllResult**.

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

Metoda **invokeApi** je volána v klientovi, který odesílá požadavek post do nového vlastního rozhraní API. Výsledek vrácený vlastním rozhraním API se zobrazí v dialogovém okně zprávy, stejně jako všechny chyby. Jiné verze **invokeApi** umožňují volitelně odeslat objekt v textu žádosti, zadat metodu HTTP a odeslat parametry dotazu s požadavkem. K dispozici jsou také netypové verze **invokeApi** .

## <a name="authentication"></a>Přidání ověřování do aplikace

Podrobné kurzy již popisují, jak tyto funkce Přidat.

App Service podporuje [ověřování uživatelů aplikací](app-service-mobile-android-get-started-users.md) pomocí různých externích zprostředkovatelů identity: Facebook, Google, účet Microsoft, Twitter a Azure Active Directory. Můžete nastavit oprávnění pro tabulky a omezit tak přístup pro konkrétní operace jenom na ověřené uživatele. Identitu ověřených uživatelů můžete také použít k implementaci autorizačních pravidel v back-endu.

Podporují se dva toky ověřování: tok **serveru** a tok **klienta** . Tok serveru poskytuje nejjednodušší možnosti ověřování, protože spoléhá na webové rozhraní zprostředkovatele identity.  K implementaci ověřování toku serveru nejsou potřeba žádné další sady SDK. Ověřování pomocí toku serveru neposkytuje hloubkovou integraci do mobilního zařízení a doporučuje se pouze pro scénáře testování konceptu.

Tok klienta umožňuje hlubší integraci s funkcemi specifickými pro zařízení, jako je jednotné přihlašování, protože spoléhá na sady SDK poskytované poskytovatelem identity.  Sadu Facebook SDK můžete například integrovat do mobilní aplikace.  Mobilní klient se zahodí do aplikace Facebook a před odchodem zpět do mobilní aplikace potvrdí vaše přihlášení.

K povolení ověřování ve vaší aplikaci je potřeba čtyři kroky:

* Zaregistrujte svoji aplikaci pro ověřování pomocí zprostředkovatele identity.
* Nakonfigurujte App Service back-end.
* Omezení oprávnění tabulky pro ověřené uživatele pouze v App Service back-endu.
* Přidejte do své aplikace ověřovací kód.

Můžete nastavit oprávnění pro tabulky a omezit tak přístup pro konkrétní operace jenom na ověřené uživatele. Pro úpravu požadavků můžete použít také identifikátor SID ověřeného uživatele.  Další informace najdete v části Začínáme [Začínáme s ověřováním] a v dokumentaci k sadě SDK serveru.

### <a name="caching"></a>Ověřování: tok serveru

Následující kód spustí proces přihlášení toku serveru pomocí poskytovatele Google.  Kvůli požadavkům na zabezpečení pro poskytovatele Google se vyžaduje další konfigurace:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Kromě toho přidejte následující metodu do hlavní třídy aktivity:

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

`GOOGLE_LOGIN_REQUEST_CODE` definovaná v hlavní aktivitě se používá pro metodu `login()` a v rámci metody `onActivityResult()`.  Můžete zvolit libovolné jedinečné číslo, pokud se používá stejné číslo v rámci metody `login()` a metody `onActivityResult()`.  Pokud jste kód klienta exportovali na adaptér služby (jak je uvedeno výše), měli byste zavolat příslušné metody v adaptéru služby.

Také je nutné nakonfigurovat projekt pro customtabs.  Nejdřív zadejte adresu URL přesměrování.  Přidejte následující fragment kódu do `AndroidManifest.xml`:

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

Přidejte **redirectUriScheme** do souboru `build.gradle` pro vaši aplikaci:

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

Nakonec přidejte `com.android.support:customtabs:28.0.0` do seznamu závislostí v souboru `build.gradle`:

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

Získejte ID přihlášeného uživatele z **MobileServiceUser** pomocí metody **GetUserID** . Příklad použití termínů pro volání rozhraní API asynchronního přihlašování najdete v tématu Začínáme [Začínáme s ověřováním].

> [!WARNING]
> Uvedené schéma URL rozlišuje malá a velká písmena.  Zajistěte, aby všechny výskyty případu `{url_scheme_of_you_app}`.

### <a name="caching"></a>Tokeny ověřování mezipaměti

Pro ukládání ověřovacích tokenů do mezipaměti je potřeba, abyste místně na zařízení ukládali ID uživatele a ověřovací token. Při příštím spuštění aplikace zkontrolujete mezipaměť a pokud jsou tyto hodnoty k dispozici, můžete přeskočit postup přihlášení a pak klienta s těmito daty dehydratované. Tato data jsou však citlivá a měla by být ukládána k zabezpečení pro případ, že se telefon odcizen.  Můžete si prohlédnout kompletní příklad ukládání tokenů ověřování do mezipaměti v [části tokeny ověřování cache][7].

Když se pokusíte použít token, jehož platnost vypršela, obdržíte *neautorizovanou odpověď 401* . Chyby ověřování můžete zpracovávat pomocí filtrů.  Filtry zachycují požadavky do back-endu App Service. Kód filtru testuje odpověď pro 401, aktivuje proces přihlášení a pak obnoví požadavek, který vygeneroval 401.

### <a name="refresh"></a>Použití aktualizačních tokenů

Token vrácený Azure App Service ověřování a autorizací má definovaný životní čas 1 hodinu.  Po uplynutí této doby je nutné znovu ověřit uživatele.  Pokud používáte dlouhotrvající token, který jste dostali přes ověřování pomocí klientského toku, můžete znovu ověřit pomocí Azure App Service ověřování a autorizace pomocí stejného tokenu.  Další Azure App Service token se vygeneruje s novou dobou platnosti.

Můžete také zaregistrovat poskytovatele pro použití aktualizačních tokenů.  Obnovovací token není vždy k dispozici.  Vyžaduje se další konfigurace:

* Pro **Azure Active Directory**nakonfigurujte tajný klíč klienta pro aplikaci Azure Active Directory.  Určuje tajný klíč klienta v Azure App Service při konfiguraci ověřování Azure Active Directory.  Při volání `.login()`, předejte `response_type=code id_token` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Pro **Google**předejte `access_type=offline` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Pro **účet Microsoft**vyberte obor `wl.offline_access`.

Chcete-li aktualizovat token, zavolejte `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Osvědčeným postupem je vytvořit filtr, který detekuje odpověď 401 ze serveru a pokusí se aktualizovat token uživatele.

## <a name="log-in-with-client-flow-authentication"></a>Přihlášení pomocí ověřování klientského toku

Obecný postup pro přihlášení pomocí ověřování klientského toku je následující:

* Nakonfigurujte Azure App Service ověřování a autorizaci, jako by to bylo ověřování v toku serveru.
* Integrujte sadu SDK poskytovatele ověřování pro ověřování a vytvořte přístupový token.
* Zavolejte metodu `.login()` následujícím způsobem (`result` by měla být `AuthenticationResult`):

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

Viz kompletní příklad kódu v následující části.

Nahraďte metodu `onSuccess()` jakýmkoli kódem, který chcete použít při úspěšném přihlášení.  `{provider}` řetězec je platný Zprostředkovatel: **AAD** (Azure Active Directory), **Facebook**, **Google**, **MicrosoftAccount**nebo **Twitter**.  Pokud jste implementovali vlastní ověřování, můžete také použít značku vlastního zprostředkovatele ověřování.

### <a name="adal"></a>Ověřování uživatelů pomocí Active Directory Authentication Library (ADAL)

K podepisování uživatelů do aplikace pomocí Azure Active Directory můžete použít Active Directory Authentication Library (ADAL). Použití přihlášení toku klienta je často vhodnější pro použití `loginAsync()` metod, protože poskytuje více nativního uživatelského prostředí a umožňuje další přizpůsobení.

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení AAD pomocí [postupu konfigurace App Service v kurzu přihlášení ke službě Active Directory][22] . Ujistěte se, že jste dokončili volitelný krok registrace nativní klientské aplikace.
2. Nainstalujte ADAL úpravou souboru Build. Gradle tak, aby zahrnoval následující definice:

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

3. Do aplikace přidejte následující kód, který provádí následující nahrazení:

    * V části pro **vložení autority** nahraďte název tenanta, ve kterém jste aplikaci zřídili. Formát by měl být https://login.microsoftonline.com/contoso.onmicrosoft.com.
    * Pro back-end mobilní aplikace nahraďte **INSERT-Resource-ID – tady** ID klienta. ID klienta můžete získat z karty **Upřesnit** v části **Nastavení Azure Active Directory** na portálu.
    * Pomocí ID klienta, které jste zkopírovali z nativní klientské aplikace, nahraďte **INSERT-Client-ID** .
    * Pomocí schématu HTTPS nahraďte **text INSERT-redirect-URI – tady** s koncovým bodem */.auth/Login/Done* vašeho webu. Tato hodnota by měla být podobná *https://contoso.azurewebsites.net/.auth/login/done* .

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

## <a name="filters"></a>Úprava komunikace mezi klientem a serverem

Připojení klienta je obvykle základní připojení HTTP pomocí základní knihovny HTTP dodávané s Android SDK.  Existuje několik důvodů, proč byste chtěli tuto změnu změnit:

* Chcete použít alternativní knihovnu HTTP pro úpravu časových limitů.
* Chcete zadat indikátor průběhu.
* Chcete přidat vlastní hlavičku pro podporu funkce API Management.
* Chcete zachytit neúspěšnou odpověď, abyste mohli implementovat opakované ověřování.
* Chcete protokolovat požadavky na back-end do služby Analytics Service.

### <a name="using-an-alternate-http-library"></a>Použití alternativní knihovny HTTP

Ihned po vytvoření odkazu na klienta volejte metodu `.setAndroidHttpClientFactory()`.  Pokud například chcete nastavit časový limit připojení na 60 sekund (místo výchozí hodnoty 10 sekund):

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

Můžete implementovat zachycení každé žádosti implementací `ServiceFilter`.  Například následující aktualizace probíhají předem vytvořeným indikátorem průběhu:

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

Tento filtr můžete připojit k klientovi následujícím způsobem:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Přizpůsobení hlaviček žádostí

Použijte následující `ServiceFilter` a připojte filtr stejným způsobem jako `ProgressFilter`:

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

### <a name="conversions"></a>Konfigurovat automatickou serializaci

Můžete zadat strategii převodu, která se vztahuje na všechny sloupce pomocí rozhraní [gson][3] API. Klientská knihovna pro Android používá [gson][3] na pozadí k serializaci objektů Java do dat JSON předtím, než se data odešlou do Azure App Service.  Následující kód používá metodu **setFieldNamingStrategy ()** k nastavení strategie. Tento příklad odstraní počáteční znak ("m") a pak malý případ dalšího znaku pro každý název pole. Například by zapnul "mId" na "ID".  Implementujte strategii převodu k omezení nutnosti `SerializedName()` poznámek ve většině polí.

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

Tento kód musí být proveden před vytvořením odkazu na mobilní klienta pomocí **MobileServiceClient**.

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
