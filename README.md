# Dependency

This library was created by .Net 8.0

## Install

```bash
dotnet add package EntityFrameworkCore.Pagination
```

## Use

```CSharp
var products = await _context.Products
                .ToPagedListAsync(pageNumber, pageSize);
```

## Result
```Csharp
{
  "datas": [
    {
      "id": 1,
      "name": "Product1",
      "price": 10
    },
    ...
  ],
  "pageNumber": 1,
  "pageSize": 15,
  "totalPages": 67,
  "isFirstPage": true,
  "isLastPage": false
}
```

## Methods

This library have one methods and one class.

invoke
```Csharp
public static class PagesListQuerableExtensions
    {
        public static async Task<PaginationResult<T>> ToPagedListAsync<T>(
            this IQueryable<T> source,
            int pageNumber,
            int pageSize,
            CancellationToken cancellationToken = default)
            where T : class
        {
            var count = await source.CountAsync();
            if(count>0) { 
            var items = await source
                .Skip((pageNumber-1) * pageSize)
                .Take(pageSize)
                .AsNoTracking()
                .ToListAsync(cancellationToken);

            return new(items,pageNumber, pageSize,count);
            }
            return new(null, 0, 0, 0);
        }

    }
```

```Csharp
public class PaginationResult<T>
    {
        public PaginationResult(IList<T> datas,int pageNumber, int pageSize, int totalCount)
        {
            Datas = datas;
            PageNumber = pageNumber;
            PageSize = pageSize;
            TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
            IsFirstPage = PageNumber == 1;
            IsLastPage = PageNumber == TotalPages;
        }
        public IList<T> Datas { get; set; }
        public int PageNumber { get; set; }
        public int PageSize { get; set; }
        public int TotalPages { get; set; }
        public bool IsFirstPage { get; set; }
        public bool IsLastPage { get; set; }
    }
```