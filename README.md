# CDK Appsync Mapping Templates

[![Built with
typescript](https://badgen.net/badge/icon/typescript?icon=typescript&label)](https://www.typescriptlang.org/)

CDK Appsync resolver mapping templates with the full range of options available.

## Why?

The CDK Appsync module comes with the built in `MappingTemplate` class, but it's
very opinionated. For example if you want to use `MappingTemplate.dynamoDbQuery`
you can't specify the query expression as from `$ctx.source`, so you can't
create nested field resolvers with it. Similarly you can't have pagination as
the `limit`/`nextToken` parameters are unavailable.

This module lets you instead specify everything directly using exactly the
format of the [AWS Appsync resolver mapping template
docs](https://docs.aws.amazon.com/appsync/latest/devguide/resolver-mapping-template-reference.html),
with the benefit of TypeScript typings and suggestions.

## Install

You must also install AWS cdk appsync as a peer dependency.

```shell
yarn install @aws-cdk/aws-appsync cdk-appsync-mapping-templates
```

## Example Use

```typescript
import MappingTemplate, {
  DynamoDBSelect,
  DynamoDBOperation,
} from 'cdk-appsync-mapping-templates';
import { Resolver } from '@aws-cdk/aws-appsync';

const requestMappingTemplate = MappingTemplate.dynamoDB({
  version: '2017-02-28',
  operation: DynamoDBOperation.QUERY,
  query: {
    expression: 'some expression',
    expressionNames: {
      '#foo': 'foo',
    },
    expressionValues: {
      ':bar': '$util.dynamodb.toDynamoDBJson($ctx.source.id)',
    },
  },
  index: 'fooIndex',
  nextToken: '$ctx.args.nextToken',
  limit: '$ctx.args.limit',
  scanIndexForward: true,
  consistentRead: false,
  select: DynamoDBSelect.ALL_ATTRIBUTES,
  filter: {
    //...
  },
});

new Resolver(this, 'dynamoDBQueryResolver', {
  //...
  requestMappingTemplate,
});
```

## Built by Skyhook

This module is contributed by the team at [Skyhook](https://www.skyhookadventure.com/).
