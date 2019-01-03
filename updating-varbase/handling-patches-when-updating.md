# Handling Patches When Updating

## Background

Most Drupal 8 distributions use a Composer patching package called [`"cweagans/composer-patches"`](https://github.com/cweagans/composer-patches). This package handles the patching mechanism nicely.

However, there are a number of problems we have had while maintaining Varbase and building sites on Varbase. Those problems are:

* There's no logging mechanism for failed patches
* Patches most of the times get included in new releases, there's no way this package can detect if the patch is already included in the new release

These issues become quite irritating and when maintaining a Drupal site, as Varbase and other Drupal distros rely on patches to work well.

Therefore, we have thought of a better way to handle patching \(it's now implemented in Varbase 8.6.3 and above\). You'll need to [update your Varbase version](https://docs.varbase.vardot.com/updating-varbase#the-update-process) to make use of this patching mechanism.

{% hint style="success" %}
Now Varbase offers a forked and upgraded version of `composer-patches` package.  
  
If you have [updated to Varbase 8.6.3 or newer](./#option-1-automated-process-using-varbase-updater-varbase-updater) it will be automatically included.  
Or see [installing Vardot's version of `composer-patches`](../getting-started/installing-varbase.md#installing-varbase-on-platform-sh) to add it manually.
{% endhint %}



## Patching Scenarios

When you execute `composer update` or `composer require vendor/xyz` command, newer versions of modules or core are downloaded. If patches for those modules are included, there are 3 possible scenarios:

<table>
  <thead>
    <tr>
      <th style="text-align:left"><b>Patch Applying Scenario</b>
      </th>
      <th style="text-align:center"><b>Patch is Still Needed?</b>
      </th>
      <th style="text-align:center"><b>Patch Will Apply?</b>
      </th>
      <th style="text-align:left"><b>Manual Action Needed?</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>1. Best Case:</b> Patch has not been included, and new version did not
        break the patch.</td>
      <td style="text-align:center"><b>Yes</b>
      </td>
      <td style="text-align:center"><b>Yes</b>
      </td>
      <td style="text-align:left"><b>No</b>.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>2. Average Case:</b> Patch has been included in the new version.</td>
      <td
      style="text-align:center"><b>No</b>
        </td>
        <td style="text-align:center"><b>No</b>
        </td>
        <td style="text-align:left">
          <p><b>Yes</b>.</p>
          <p>Remove the patch from <code>composer.json</code>
          </p>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>3. Worst Case:</b> Patch has not been included, and new version did
        break the patch.</td>
      <td style="text-align:center"><b>Yes</b>
      </td>
      <td style="text-align:center"><b>No</b>
      </td>
      <td style="text-align:left"><b>Yes</b>.
        <br />Manually re-roll the patch, handle conflict, and fix it yourself</td>
    </tr>
  </tbody>
</table>### 

### How Vardot Will Handle Those Cases

Based on the scenarios mentioned above, we have forked the `"cweagans/composer-patches"` package, and added an Event Dispatcher on patch failure. The Event Dispatcher will dispatch an Event that will prompt you to perform an action depending on the scenarios above. Thus:

{% hint style="success" %}
#### **Best Case:** Patch has not been included, and new version did not break the patch.

Nothing will happen.
{% endhint %}

{% hint style="warning" %}
#### **Average Case:** Patch has been included in the new version.

The Varbase Updater will detect that the patch has been applied and ask you if you want to remove it from your `composer.json`
{% endhint %}

{% hint style="danger" %}
#### **Worst Case:** Patch has not been included, and new version did break the patch.

The Varbase Updater will log failed patches to a file named `failed-patches.txt`  
You can review it and try to handle those patches yourself.
{% endhint %}



## Installing Vardot's composer-patches Package

If you're using Varbase 8.6.3 or greater, just run `composer update` and it will work.

If you're using Varbase 8.6.2 or older, you can [update to the most recent version of Varbase 8.6.x](https://docs.varbase.vardot.com/updating-varbase#the-update-process) and make use of this functionality. Or run the following commands:

1. Add Vardot's composer-patches repository to use our forked package instead of the cweagans's. `composer config repositories.composer-patches vcs https://github.com/vardot/composer-patches`
2. Run `composer update` to get the new package.
3. Run `composer update` again to update your packages and start applying the new patching mechanism.
