# Remix.java #

**See also:** [bestbuy-service-remix](http://code.google.com/p/bestbuy-service-remix/) (PHP)

Remix.java is a Java library for making calls to <a href='http://www.bestbuy.com'>Best Buy</a>'s <a href='http://remix.bestbuy.com/'>Remix</a> API.

Best Buy provides <a href='http://remix.bestbuy.com/docs'>API documentation</a>, <a href='http://remix.bestbuy.com/forum'>forums</a>, and related resources on the Remix <a href='http://remix.bestbuy.com/'>developer network</a> site.

The purpose of this library is to simplify interaction with the Remix API in the context of a Java application.

## Example usage ##

Simple example (no error checking = bad):
```
import com.mattwilliamsnyc.service.remix.*;

public class RemixDemo {

    public static void main(String[] args) {
        Remix remix = new Remix("YourApiKey");
        for(Store store : remix.getStores().list()) {
            System.out.print(store.getName());
            System.out.println(store.getPhone());
            System.out.println(store.getHours());
            System.out.println();
        }
    }
}
```
More complete example, returns a list of stores within 10 miles of postal code 11201 with a `PlayStation 3` available:
```
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import com.mattwilliamsnyc.service.remix.*;

public class RemixAdvancedDemo {

    public static void main(String[] args) {
        Remix remix = new Remix("YourApiKey");

        List<String> storeFilters   = new ArrayList<String>();
        List<String> productFilters = new ArrayList<String>();

        storeFilters.add("area(11201,10)");
        productFilters.add("sku=8982988");

        try {
            StoresResponse response = remix.getStoreAvailability(
                storeFilters,
                productFilters
            );

            if(!response.isError()) {
                for(Store store : response.list()) {
                    System.out.println(
                        store.getName() + " (" + store.getDistance() + " miles)"
                    );
                    for(Product product : store.getProducts()) {
                        if(product.hasInStoreAvailability()) {
                            System.out.println(product.getName());
                            System.out.println(
                                "Available for $" + product.getSalePrice()
                            );
                        }
                    }
                    System.out.println();
                }
            } else {
                ErrorDocument error = response.getError();
                if(null != error) {
                    System.out.println(error.getStatus());
                    System.out.println(error.getMessage());
                    System.out.println("Examples:");
                    for(String example : error.getExamples()) {
                        System.out.println(example);
                    }
                }
            }
        } catch(RemixException e) {
            e.printBackTrace();
        }
    }
}
```