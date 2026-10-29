<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import { groupByCause, uniqueById, type Cause, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  const ticket = 4471;
  const exported = reports as Report[];
  const merged = uniqueById(exported).filter((r) => r.mergedInto === ticket);
  const groups = groupByCause(merged, tenantOf);

  const issues: Record<Cause, { title: string; url: string }> = {
    parser: {
      title: 'Shared parser reads day-first dates as month-first',
      url: 'https://github.com/PaulinaSadowskaAllegro/mega-dev-date-reports/issues/1',
    },
    hint: {
      title: 'booking-v3 date hint conflicts with day-first locales',
      url: 'https://github.com/PaulinaSadowskaAllegro/mega-dev-date-reports/issues/2',
    },
  };

  const tags = (taggedAs: Record<string, number>) =>
    Object.entries(taggedAs)
      .sort((a, b) => b[1] - a[1])
      .map(([tag, count]) => `${tag} ×${count}`)
      .join(' · ');
</script>

<p class="sum">
  <strong>{exported.length}</strong> exported rows ·
  <strong>{exported.length - merged.length}</strong> repeated rows removed ·
  <strong>{merged.length}</strong> unique reports
</p>

<table>
  <thead>
    <tr><th>problem</th><th>reports</th><th>tenants</th><th>intake tagged as</th></tr>
  </thead>
  <tbody>
    {#each groups as group (group.cause)}
      {@const issue = group.cause === 'unplaced' ? null : issues[group.cause]}
      <tr>
        <td>
          {#if issue}<a href={issue.url}>{issue.title}</a>{:else}Unplaced reports{/if}
        </td>
        <td>{group.reports.length}</td>
        <td>{group.tenants}</td>
        <td class="tags">{tags(group.taggedAs)}</td>
      </tr>
    {/each}
  </tbody>
</table>

<style>
  .sum { font-size: 1.1rem; }
  .tags { color: var(--muted); font-size: 0.85em; }
</style>
