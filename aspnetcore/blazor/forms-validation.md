---
title: Blazor フォームと検証の ASP.NET Core
author: guardrex
description: Blazor でフォームとフィールドの検証シナリオを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/forms-validation
ms.openlocfilehash: 0b2e38cdbd974a28960b917fb6b5ce370f8c4659
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030326"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="4c292-103">Blazor フォームと検証の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c292-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="4c292-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4c292-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4c292-105">[データ注釈](xref:mvc/models/validation)を使用して、Blazor でフォームおよび検証をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4c292-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="4c292-106">フォームと検証のシナリオは、プレビューリリースごとに変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4c292-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="4c292-107">次`ExampleModel`の型は、データ注釈を使用して検証ロジックを定義します。</span><span class="sxs-lookup"><span data-stu-id="4c292-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="4c292-108">フォームは、 `EditForm`コンポーネントを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="4c292-108">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="4c292-109">次のフォームは、一般的な要素、コンポーネント、および Razor コードを示しています。</span><span class="sxs-lookup"><span data-stu-id="4c292-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

* <span data-ttu-id="4c292-110">フォームは、 `ExampleModel`型で定義さ`name`れた検証を使用して、フィールドのユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="4c292-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="4c292-111">モデルはコンポーネントの`@code`ブロック内に作成され、プライベートフィールド (`exampleModel`) に保持されます。</span><span class="sxs-lookup"><span data-stu-id="4c292-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="4c292-112">フィールドは、 `Model` `<EditForm>`要素の属性に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="4c292-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="4c292-113">コンポーネント`DataAnnotationsValidator`は、データ注釈を使用して検証サポートをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="4c292-113">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="4c292-114">コンポーネント`ValidationSummary`は、検証メッセージの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="4c292-114">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="4c292-115">`HandleValidSubmit`は、フォームが正常に送信された (検証に合格した) ときにトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="4c292-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="4c292-116">ユーザー入力の受信と検証には、一連の組み込みの入力コンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="4c292-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="4c292-117">入力は、変更されたときとフォームが送信されたときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="4c292-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="4c292-118">次の表に、使用できる入力コンポーネントを示します。</span><span class="sxs-lookup"><span data-stu-id="4c292-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="4c292-119">入力コンポーネント</span><span class="sxs-lookup"><span data-stu-id="4c292-119">Input component</span></span> | <span data-ttu-id="4c292-120">表示形式&hellip;</span><span class="sxs-lookup"><span data-stu-id="4c292-120">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="4c292-121">を含む`EditForm`すべての入力コンポーネントは、任意の属性をサポートします。</span><span class="sxs-lookup"><span data-stu-id="4c292-121">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="4c292-122">コンポーネントパラメーターに一致しない属性は、表示される HTML 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4c292-122">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="4c292-123">入力コンポーネントは、編集時に検証を行い、フィールドの状態を反映するように CSS クラスを変更するための既定の動作を提供します。</span><span class="sxs-lookup"><span data-stu-id="4c292-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="4c292-124">一部のコンポーネントには、役に立つ解析ロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4c292-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="4c292-125">たとえば、と`InputDate` `InputNumber`は、検証エラーとして登録することによって、解析不能な値を適切に処理します。</span><span class="sxs-lookup"><span data-stu-id="4c292-125">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="4c292-126">Null 値を受け入れることができる型では、ターゲットフィールドの null 値の`int?`許容もサポートされます (たとえば、)。</span><span class="sxs-lookup"><span data-stu-id="4c292-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="4c292-127">次`Starship`の型では、以前`ExampleModel`よりも大きなプロパティとデータ注釈を使用して検証ロジックを定義しています。</span><span class="sxs-lookup"><span data-stu-id="4c292-127">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="4c292-128">前の例では`Description` 、データ注釈が存在しないため、は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="4c292-128">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="4c292-129">次のフォームは、 `Starship`モデルで定義されている検証を使用してユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="4c292-129">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="4c292-130">は`EditForm` 、変更`EditContext`されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components#cascading-values-and-parameters)としてを作成します。</span><span class="sxs-lookup"><span data-stu-id="4c292-130">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="4c292-131">また`EditForm` 、は、有効かつ無効な送信 (`OnValidSubmit`, `OnInvalidSubmit`) の便利なイベントも提供します。</span><span class="sxs-lookup"><span data-stu-id="4c292-131">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="4c292-132">または、 `OnSubmit`を使用して、検証フィールドとチェックフィールドの値をカスタム検証コードでトリガーします。</span><span class="sxs-lookup"><span data-stu-id="4c292-132">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="4c292-133">コンポーネント`DataAnnotationsValidator`は、データ注釈をカスケード`EditContext`に使用して検証サポートをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="4c292-133">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="4c292-134">現在、データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要ですが、この動作を既定の動作に設定することを検討しています。</span><span class="sxs-lookup"><span data-stu-id="4c292-134">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="4c292-135">データ注釈とは異なる検証システムを使用するには`DataAnnotationsValidator` 、をカスタム実装に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4c292-135">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="4c292-136">ASP.NET Core の実装は、参照ソースの検査に使用できます。[DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="4c292-136">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="4c292-137">*ASP.NET Core の実装は、プレビューリリース期間中に迅速な更新が適用されます。*</span><span class="sxs-lookup"><span data-stu-id="4c292-137">*The ASP.NET Core implementation is subject to rapid updates during the preview release period.*</span></span>

<span data-ttu-id="4c292-138">コンポーネント`ValidationSummary`は、検証の[概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)に似たすべての検証メッセージを要約します。</span><span class="sxs-lookup"><span data-stu-id="4c292-138">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="4c292-139">コンポーネント`ValidationMessage`には、[検証メッセージタグヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似た、特定のフィールドの検証メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4c292-139">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="4c292-140">`For`属性を使用した検証用のフィールドと、モデルプロパティに名前を付けるラムダ式を指定します。</span><span class="sxs-lookup"><span data-stu-id="4c292-140">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="4c292-141">コンポーネント`ValidationMessage` と`ValidationSummary`コンポーネントは、任意の属性をサポートします。</span><span class="sxs-lookup"><span data-stu-id="4c292-141">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="4c292-142">コンポーネントパラメーターに一致しない属性は、生成`<div>`される要素また`<ul>`は要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4c292-142">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>