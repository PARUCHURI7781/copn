# copn



import boto3
from botocore.exceptions import ClientError

PROFILE_NAME = "331875467123_Entergy_Gov_DataCore_DE_DEV"
REGION = "us-gov-east-1"
TABLE_NAME = "etl_metadata_test"

def main():
    # Create a session using your AWS SSO profile
    session = boto3.Session(profile_name=PROFILE_NAME)
    dynamodb = session.resource("dynamodb", region_name=REGION)

    print("📋 Listing existing DynamoDB tables:")
    for tname in dynamodb.tables.all():
        print("  -", tname.name)

    existing = [t.name for t in dynamodb.tables.all()]
    if TABLE_NAME not in existing:
        print(f"\n🧱 Creating new DynamoDB table: {TABLE_NAME}")
        try:
            table = dynamodb.create_table(
                TableName=TABLE_NAME,
                KeySchema=[
                    {"AttributeName": "db_source", "KeyType": "HASH"},
                    {"AttributeName": "table_name", "KeyType": "RANGE"}
                ],
                AttributeDefinitions=[
                    {"AttributeName": "db_source", "AttributeType": "S"},
                    {"AttributeName": "table_name", "AttributeType": "S"}
                ],
                BillingMode="PAY_PER_REQUEST"
            )
            table.wait_until_exists()
            print(f"✅ Table {TABLE_NAME} created successfully.")
        except ClientError as e:
            print("⚠️ Table creation failed:", e)
    else:
        print(f"\n✅ Table {TABLE_NAME} already exists — skipping creation.")

    # Put one test item
    table = dynamodb.Table(TABLE_NAME)
    print("\n📤 Writing one test record...")
    table.put_item(
        Item={
            "db_source": "sales",
            "table_name": "orders",
            "last_cdc_load_date": "2025-10-11"
        }
    )
    print("✅ Item inserted.")

    print("\n📥 Reading it back from DynamoDB...")
    response = table.get_item(
        Key={"db_source": "sales", "table_name": "orders"}
    )
    print("🔎 Item retrieved:", response.get("Item"))

if __name__ == "__main__":
    try:
        main()
    except ClientError as e:
        print("❌ AWS error:", e)
    except Exception as ex:
        print("❌ Unexpected error:", ex)
