# Integration Testing for CDK Projects

## Validate Templates

```bash
# Synthesize all stacks
cdk synth

# Check diff against deployed stacks
cdk diff

# Check diff for a specific stack
cdk diff <StackName>
```

## Deploy to Test Environment

```bash
# Deploy to staging/test account
cdk deploy --all --require-approval broadening

# Deploy a specific stack
cdk deploy <StackName>

# Deploy with context overrides
cdk deploy --context env=staging
```

## Cleanup

```bash
# Destroy test stacks
cdk destroy --all

# Destroy a specific stack
cdk destroy <StackName>
```

## Snapshot Testing with Jest

```typescript
import { Template } from 'aws-cdk-lib/assertions';

test('snapshot test', () => {
  const template = Template.fromStack(stack);
  expect(template.toJSON()).toMatchSnapshot();
});
```

Update snapshots after intentional changes:
```bash
npx jest --updateSnapshot
```
