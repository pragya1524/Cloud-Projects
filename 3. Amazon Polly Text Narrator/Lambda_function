import json
import boto3
import time

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    polly = boto3.client('polly')

    try:
        bucket = event['Records'][0]['s3']['bucket']['name']
        key = event['Records'][0]['s3']['object']['key']

        print(f"Triggered by file: {key} in bucket: {bucket}")

        # 👇 Add delay to ensure S3 object is fully available
        time.sleep(10)

        # Read text from S3
        response = s3.get_object(Bucket=bucket, Key=key)
        text = response['Body'].read().decode('utf-8')

        print("Text content read successfully.")

        # Convert to speech
        polly_response = polly.synthesize_speech(
            Text=text,
            OutputFormat='mp3',
            VoiceId='Joanna'
        )

        # Save MP3 to S3
        output_key = key.rsplit('.', 1)[0] + '.mp3'
        s3.put_object(
            Bucket=bucket,
            Key=output_key,
            Body=polly_response['AudioStream'].read()
        )

        print(f"MP3 saved to: {output_key}")
        return {'statusCode': 200, 'body': f"Audio saved as {output_key}"}

    except Exception as e:
        print(f"Error: {str(e)}")
        return {'statusCode': 500, 'body': str(e)}
